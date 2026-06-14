---
id: a1b2c3d4-1041-4000-8000-000000000041
title: Hybrid Search for RAG
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001041
---
# Hybrid Search for RAG

Hybrid search combines dense (semantic) and sparse (keyword) retrieval to get the best of both approaches — capturing meaning AND matching exact terms.

## Dense vs Sparse Retrieval

| Aspect | Dense (Embedding) | Sparse (BM25/TF-IDF) |
|--------|-------------------|----------------------|
| Matching | Semantic similarity | Exact keyword match |
| Out-of-vocabulary | Handles synonyms well | Misses paraphrases |
| Rare terms | May ignore rare terms | Excels at rare terms |
| Latency | Higher (ANN search) | Lower (inverted index) |
| Storage | Vector index (large) | Inverted index (small) |
| Cold start | Needs training data | Works immediately |

## Why Hybrid?

```
Query: "How to change a flat tire on a sedan?"

BM25 matches: "flat", "tire", "sedan", "change"
Embedding captures: "puncture", "jack", "spare wheel", "repair"

Combined: finds docs with both exact keyword hits AND semantic relevance
```

## Fusion Strategies

### Reciprocal Rank Fusion (RRF)

```python
def rrf(results_dense, results_sparse, k=60):
    scores = {}
    for rank, doc_id in enumerate(results_dense):
        scores[doc_id] = scores.get(doc_id, 0) + 1 / (k + rank)
    for rank, doc_id in enumerate(results_sparse):
        scores[doc_id] = scores.get(doc_id, 0) + 1 / (k + rank)
    return sorted(scores, key=scores.get, reverse=True)
```

- **k**: Smoothing constant (typical 60)
- **Pros**: No training needed, robust
- **Cons**: Equal weight to both systems

### Weighted Sum

```python
score = α * dense_score + (1-α) * sparse_score
```

- **α**: Tuning parameter (0.3-0.7 typical)
- Scores must be normalized to comparable ranges (min-max or z-score)

### Learning to Rank

Train a model (XGBoost, LambdaMART) on features from both systems:

| Feature | Source |
|---------|--------|
| Dense similarity | Embedding cosine score |
| BM25 score | Sparse retrieval |
| Query-doc overlap | Number of shared terms |
| Document popularity | Click-through rate |

## Implementation Approaches

### Two-Stage Pipeline

```
Query → Dense + Sparse (parallel) → RRF → Top-K → LLM
```

### Single Index Approaches

| System | Method |
|--------|--------|
| Elasticsearch | `script_score` with combination of `_score` (BM25) and `cosineSimilarity` |
| Vespa | Built-in `wand` with hybrid scoring |
| Weaviate | Hybrid search natively supported (bm25 + vector) |
| Pinecone | Separate indexes + client-side fusion |
| Qdrant | `prefetch` with multiple queries and RRF |

## Query Translation

Improve hybrid search by expanding queries:

- **Query rewriting**: LLM reformulates the query for better BM25 matching
- **Query decomposition**: Break complex queries into sub-queries
- **HyDE (Hypothetical Document Embedding)**: Generate a hypothetical perfect document, use its embedding

## When Hybrid Struggles

| Scenario | Issue |
|----------|-------|
| Highly specialized jargon | BM25 overweights rare terms |
| Very short queries | Both systems have limited signal |
| Multi-lingual | BM25 doesn't handle translation |
| Polysemy | "Apple" (fruit vs company) |

**Links**: [[Retrieval Strategies]] | [[RAG Architecture]] | [[Embedding Models for RAG]] | [[Reranking]] | [[Vector Databases for RAG]]
