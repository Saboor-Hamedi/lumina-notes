---
id: rag-009-0000-0000-0000-000000000009
title: Evaluation of RAG Systems
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781700000009
---
# Evaluation of RAG Systems

**Links**: [[RAG Architecture]] | [[Reranking]] | [[Advanced RAG Patterns]] | [[Retrieval Strategies]] | [[Prompt Engineering for RAG]]

## Retrieval Metrics

| Metric | What It Measures |
|--------|-----------------|
| **Recall@K** | Fraction of relevant docs retrieved |
| **Precision@K** | Fraction of retrieved docs that are relevant |
| **MRR** | Rank of the first relevant result |
| **NDCG** | Ranking quality with graded relevance |

```python
def recall_at_k(relevant, retrieved, k):
    return len(set(retrieved[:k]) & set(relevant)) / len(relevant)
```

## Generation Metrics

| Metric | What It Measures |
|--------|-----------------|
| **Faithfulness** | Answer grounded in context |
| **Answer relevancy** | Answer addresses the question |
| **Context relevance** | Retrieved context is useful |
| **Hallucination rate** | Facts not in context |

## RAGAS Framework

```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy

results = evaluate(dataset, metrics=[faithfulness, answer_relevancy])
```

## LLM-as-Judge

```python
def evaluate_faithfulness(question, answer, context):
    prompt = f"""Rate 1-5: Does the answer stick to the context?
Context: {context}
Answer: {answer}
Score:"""
    return parse_score(llm.generate(prompt))
```

**Next**: [[RAG vs Fine-tuning]] — When to use each