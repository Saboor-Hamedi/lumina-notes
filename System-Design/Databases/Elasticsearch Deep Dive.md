---
id: a1b2c3d4-1182-4000-8000-000000000182
title: Elasticsearch Deep Dive
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001182
---
---
id: a1b2c3d4-1182-4000-8000-000000000182
title: Elasticsearch Deep Dive
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001182
---

# Elasticsearch Deep Dive

**Links**: [[Search Engines]] | [[Vector Databases for RAG]] | [[Database Indexing Deep Dive]] | [[Database Sharding]] | [[Logging Best Practices]] | [[Monitoring and Observability]]

**See also**: [[Apache Kafka Deep Dive]] (log ingestion), [[Hybrid Search for RAG]], [[Database Replication Strategies]]

Elasticsearch is a distributed search and analytics engine built on Apache Lucene. It provides full-text search, structured search, aggregations, and real-time analytics.

## Architecture

```
Cluster
  ├── Node 1 (Master-eligible)
  │    ├── Index A (Primary Shard 0)
  │    ├── Index A (Replica Shard 1)
  │    └── Index B (Primary Shard 1)
  │
  ├── Node 2 (Data)
  │    ├── Index A (Primary Shard 1)
  │    ├── Index A (Replica Shard 0)
  │    └── Index B (Primary Shard 0)
  │
  └── Node 3 (Ingest + Data)
       ├── Index A (Replica Shard 2)
       └── Index B (Replica Shard 1)
```

## Basic Operations

```json
// Index a document
PUT /products/_doc/1
{
    "name": "Wireless Mouse",
    "description": "Ergonomic wireless mouse with USB-C charging",
    "price": 29.99,
    "category": "electronics",
    "tags": ["mouse", "wireless", "ergonomic"],
    "in_stock": true,
    "created_at": "2026-06-13T10:00:00Z"
}

// Get document
GET /products/_doc/1

// Search
GET /products/_search
{
    "query": {
        "match": {
            "description": "wireless ergonomic mouse"
        }
    },
    "sort": [{ "price": "asc" }],
    "from": 0,
    "size": 20
}
```

## Mapping

```json
// Explicit mapping
PUT /products
{
    "mappings": {
        "properties": {
            "name": {
                "type": "text",
                "fields": {
                    "keyword": { "type": "keyword" }
                }
            },
            "description": {
                "type": "text",
                "analyzer": "english"
            },
            "price": { "type": "float" },
            "category": { "type": "keyword" },
            "tags": { "type": "keyword" },
            "in_stock": { "type": "boolean" },
            "created_at": { "type": "date" },
            "location": { "type": "geo_point" },
            "embedding": {
                "type": "dense_vector",
                "dims": 384,
                "similarity": "cosine"
            }
        }
    }
}
```

## Query Types

```json
// Full-text search
{
    "query": {
        "match": {
            "description": "ergonomic mouse"
        }
    }
}

// Exact match (keyword)
{
    "query": {
        "term": {
            "category": "electronics"
        }
    }
}

// Boolean query
{
    "query": {
        "bool": {
            "must": [
                { "match": { "description": "wireless mouse" } }
            ],
            "filter": [
                { "term": { "in_stock": true } },
                { "range": { "price": { "gte": 10, "lte": 50 } } }
            ],
            "should": [
                { "term": { "tags": "ergonomic" } }
            ],
            "must_not": [
                { "term": { "category": "accessories" } }
            ]
        }
    }
}

// Fuzzy search
{
    "query": {
        "fuzzy": {
            "name": {
                "value": "mous",
                "fuzziness": "AUTO"
            }
        }
    }
}

// Phrase search
{
    "query": {
        "match_phrase": {
            "description": "USB-C charging"
        }
    }
}

// Multi-field search
{
    "query": {
        "multi_match": {
            "query": "wireless ergonomic",
            "fields": ["name^2", "description", "tags"]
        }
    }
}
```

## Aggregations

```json
// Price range + average by category
{
    "size": 0,
    "aggs": {
        "by_category": {
            "terms": { "field": "category" },
            "aggs": {
                "avg_price": { "avg": { "field": "price" } },
                "price_range": {
                    "range": {
                        "field": "price",
                        "ranges": [
                            { "to": 20 },
                            { "from": 20, "to": 50 },
                            { "from": 50 }
                        ]
                    }
                }
            }
        }
    }
}

// Date histogram
{
    "aggs": {
        "orders_over_time": {
            "date_histogram": {
                "field": "created_at",
                "calendar_interval": "day"
            }
        }
    }
}
```

## Indexing Performance

```json
// Bulk API — much faster than individual indexing
POST /_bulk
{ "index": { "_index": "products", "_id": "1" } }
{ "name": "Product 1", "price": 10.0 }
{ "index": { "_index": "products", "_id": "2" } }
{ "name": "Product 2", "price": 20.0 }

// Tuning for bulk indexing
PUT /products/_settings
{
    "index": {
        "refresh_interval": "-1",         // Disable refresh during bulk
        "number_of_replicas": 0,          // No replicas during bulk
        "translog.durability": "async"    // Async translog
    }
}

// Re-enable after bulk
PUT /products/_settings
{
    "index": {
        "refresh_interval": "1s",
        "number_of_replicas": 1,
        "translog.durability": "request"
    }
}
```

## Cluster Management

```json
// Health
GET _cluster/health

// Node info
GET _nodes/stats

// Index stats
GET /products/_stats

// Pending tasks
GET _cluster/pending_tasks

// Hot threads (CPU profiling)
GET _nodes/hot_threads

// Cat APIs
GET _cat/indices?v
GET _cat/shards?v
GET _cat/nodes?v
```

## Text Analysis

```json
// Custom analyzer
PUT /products
{
    "settings": {
        "analysis": {
            "analyzer": {
                "custom_english": {
                    "type": "standard",
                    "tokenizer": "standard",
                    "filter": [
                        "lowercase",
                        "stop",
                        "porter_stem",
                        "asciifolding"
                    ]
                }
            }
        }
    }
}

// Test analyzer
POST /products/_analyze
{
    "analyzer": "custom_english",
    "text": "The running foxes were jumping quickly!"
}
// Tokens: [run, fox, jump, quick]
```

## Index Lifecycle Management

```json
PUT _ilm/policy/logs_policy
{
    "policy": {
        "phases": {
            "hot": {
                "min_age": "0ms",
                "actions": {
                    "rollover": { "max_size": "50GB", "max_age": "7d" }
                }
            },
            "warm": {
                "min_age": "30d",
                "actions": {
                    "shrink": { "number_of_shards": 1 },
                    "forcemerge": { "max_num_segments": 1 }
                }
            },
            "cold": {
                "min_age": "90d",
                "actions": {
                    "freeze": {}
                }
            },
            "delete": {
                "min_age": "365d",
                "actions": {
                    "delete": {}
                }
            }
        }
    }
}
```
