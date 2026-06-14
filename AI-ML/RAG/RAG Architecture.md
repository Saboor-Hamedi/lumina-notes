---
id: rag-001-0000-0000-0000-000000000001
title: RAG Architecture
language: markdown
tags: [ai-ml, rag, architecture]
selection: null
isPinned: true
timestamp: 1781700000001
---
# RAG Architecture

**Links**: [[Text Embedding Models]] | [[Chunking Strategies]] | [[Vector Databases for RAG]] | [[Retrieval Strategies]] | [[Reranking]] | [[Prompt Engineering for RAG]] | [[Advanced RAG Patterns]] | [[Evaluation of RAG Systems]]

## What is RAG?

Retrieval-Augmented Generation (RAG) combines information retrieval with a generative language model. Instead of relying solely on the model's parametric knowledge, RAG fetches relevant documents from an external knowledge base and feeds them as context to the LLM.

## Basic RAG Flow

```
User Query
    │
    ▼
┌──────────────────┐
│   Embed Query     │ ──→ Query Vector
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Vector Search DB  │ ──→ Top-K Relevant Chunks
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Build Prompt     │ ← Query + Chunks + Instructions
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│   LLM Generate    │ ──→ Grounded Answer
└──────────────────┘
```

## Core Components

| Component | Role | Technologies |
|-----------|------|--------------|
| **Embedding Model** | Converts text to vectors | text-embedding-3-small, BGE, E5 |
| **Vector Database** | Stores and searches embeddings | Qdrant, Pinecone, pgvector, Weaviate |
| **Retriever** | Fetches relevant documents | Dense, Sparse, Hybrid |
| **Reranker** | Re-ranks retrieved docs | Cohere Rerank, Cross-encoders |
| **LLM** | Generates final answer | GPT-4, Claude, Llama, Mistral |

## Why RAG?

| Approach | Knowledge Currency | Hallucination | Customization |
|----------|-------------------|----------------|---------------|
| Base LLM | Static (training cutoff) | High | None |
| Fine-tuning | Static + expensive | Medium | High |
| **RAG** | **Dynamic** | **Low** | **Medium** |
| Agent + Tools | Fully dynamic | Low | Very high |

## Design Decisions

- **Chunk size**: 256-1024 tokens (depends on document type)
- **Top-K**: 3-10 chunks per query
- **Embedding model**: 768-1536 dimensions
- **Search type**: Dense (semantic), Sparse (keyword), or Hybrid
- **Prompt template**: Structured context + instructions + query

**Next**: [[Chunking Strategies]] — How to split documents