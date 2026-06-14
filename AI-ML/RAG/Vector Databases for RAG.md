---
id: rag-003-0000-0000-0000-000000000003
title: Vector Databases for RAG
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781700000003
---
# Vector Databases for RAG

**Links**: [[RAG Architecture]] | [[Embedding Models for RAG]] | [[Retrieval Strategies]] | [[Database Indexing Deep Dive]] | [[Database Engines Compared]]

## What is a Vector Database?

A vector database indexes, stores, and retrieves vector embeddings using Approximate Nearest Neighbor (ANN) search. Unlike traditional databases, it finds items by semantic similarity rather than exact matches.

## Popular Vector Databases

| Database | Open Source | Cloud | Special Features |
|----------|-------------|-------|------------------|
| **pgvector** | Yes | Any PostgreSQL | SQL + vectors, ACID |
| **Qdrant** | Yes | Qdrant Cloud | Payload filtering, quantization |
| **Weaviate** | Yes | Weaviate Cloud | GraphQL, hybrid search, modules |
| **Pinecone** | No | Pinecone | Fully managed, serverless |
| **Milvus** | Yes | Zilliz Cloud | GPU-accelerated, distributed |

## ANN Index Types

| Index | Speed | Recall | Memory | Build |
|-------|-------|--------|--------|-------|
| **HNSW** | Fastest | Highest | High | Slow |
| **IVF** | Fast | Medium | Medium | Fast |
| **IVF+PQ** | Medium | Medium | Low | Fast |
| **DiskANN** | Medium | High | Low | Medium |

## pgvector Setup

```sql
CREATE EXTENSION vector;

CREATE TABLE documents (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    content TEXT NOT NULL,
    embedding vector(1536)
);

CREATE INDEX ON documents USING hnsw (embedding vector_cosine_ops);

SELECT content, 1 - (embedding <=> query_embedding) AS similarity
FROM documents
ORDER BY embedding <=> query_embedding
LIMIT 5;
```

## Qdrant with Filtering

```python
from qdrant_client import QdrantClient, models

client = QdrantClient(":memory:")

results = client.search(
    collection_name="docs",
    query_vector=query_vector,
    query_filter=models.Filter(
        must=[
            models.FieldCondition(
                key="category",
                match=models.MatchValue(value="programming")
            )
        ]
    ),
    limit=10
)
```

**Next**: [[Embedding Models for RAG]] — Choose the right model