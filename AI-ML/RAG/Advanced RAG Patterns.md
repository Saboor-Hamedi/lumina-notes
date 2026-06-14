---
id: rag-008-0000-0000-0000-000000000008
title: Advanced RAG Patterns
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781700000008
---
# Advanced RAG Patterns

**Links**: [[RAG Architecture]] | [[Prompt Engineering for RAG]] | [[Retrieval Strategies]] | [[Reranking]] | [[Evaluation of RAG Systems]] | [[LLM Agents Framework]]

## Corrective RAG (CRAG)

Assess relevance before generating, re-retrieve if needed.

```python
def corrective_rag(query):
    chunks = retrieve(query)
    scores = relevance_score(query, chunks)
    if max(scores) > 0.7:
        return generate(query, chunks)
    new_chunks = retrieve(query, broader=True)
    if max(scores) > 0.3:
        return generate(query, new_chunks)
    return "I don't have enough information."
```

## Self-RAG

The LLM decides when to retrieve and reflects on its own outputs — retrieve only when confidence is low.

## Agentic RAG

An agent orchestrates search, database queries, calculations, and tool use.

```python
class RAGAgent:
    def answer(self, query):
        sub_queries = self.decompose(query)
        contexts = [self.retrieve(q) for q in sub_queries]
        if "stats" in query:
            data = self.query_database(query)
        plan = self.plan(contexts, data)
        return self.execute(plan)
```

## HyDE (Hypothetical Document Embeddings)

Generate a hypothetical answer, embed it, use that embedding for retrieval.

```python
hypo_doc = llm.generate(f"Answer: {query}")
hypo_vec = embed(hypo_doc)
results = vector_db.search(hypo_vec, top_k=5)
```

## Graph RAG

Extracts entities and relationships into a knowledge graph for multi-hop reasoning.

**Next**: [[Evaluation of RAG Systems]] — Measure RAG quality