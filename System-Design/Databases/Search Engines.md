---
id: a1b2c3d4-1030-4000-8000-000000000030
title: Search Engines
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001030
---
# Search Engines

Search engines index, store, and retrieve information. Elasticsearch (backed by Apache Lucene) is the dominant open-source search engine.

## Inverted Index

The core data structure: maps terms → documents containing them.

```
Document 1: "the quick brown fox"
Document 2: "the lazy brown dog"

Inverted Index:
"the"   → [doc1, doc2]
"quick" → [doc1]
"brown" → [doc1, doc2]
"fox"   → [doc1]
"lazy"  → [doc2]
"dog"   → [doc2]
```

## Relevance Scoring (TF-IDF & BM25)

| Concept | Meaning |
|---------|---------|
| TF (Term Frequency) | How often term appears in document |
| IDF (Inverse Document Frequency) | How rare term is across all docs |
| BM25 | Modern evolution of TF-IDF (Lucene default) |

```
score = BM25(query, document)
Higher = more relevant
```

## Elasticsearch Stack

```
┌──────────────┐
│  Kibana      │  Visualization & dashboard
├──────────────┤
│  Elasticsearch │  Search & analytics engine
├──────────────┤
│  Logstash / │
│  Beats      │  Data ingestion
└──────────────┘
```

## Key Concepts

| Concept | Description |
|---------|-------------|
| Index | Collection of documents (like DB table) |
| Document | JSON record (like DB row) |
| Mapping | Schema definition (field types, analyzers) |
| Shard | Horizontal partition of an index |
| Replica | Copy of a shard for redundancy |
| Analyzer | Tokenizer + token filters for text processing |

## Search Types

```json
// Full-text search
{ "match": { "title": "brown fox" } }

// Exact value
{ "term": { "status": "published" } }

// Compound
{
  "bool": {
    "must":     { "match": { "title": "fox" } },
    "filter":   { "term": { "status": "published" } },
    "should":   { "match": { "content": "quick" } },
    "must_not": { "term": { "deleted": true } }
  }
}

// Aggregation (analytics)
{
  "aggs": {
    "by_status": { "terms": { "field": "status" } }
  }
}
```

## Indexing Pipeline

```
Document → Analyzer → Terms → Inverted Index
                        ↓
                  Stored Fields (for retrieval)
```

## Performance Optimization

- Use `filter` context (cached, no scoring) for yes/no queries
- Limit fields returned in `_source`
- Use `keyword` fields for exact matches, sorting, aggregations
- Shard size: 10-50 GB per shard optimal
- Hot-warm-cold tier architecture

**Links**: [[Vector Databases for RAG]] | [[RAG Architecture]] | [[SQL Query Optimization]] | [[Database Indexing Deep Dive]] | [[PostgreSQL Extensions]]
