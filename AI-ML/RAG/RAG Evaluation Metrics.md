---
id: a1b2c3d4-1045-4000-8000-000000000045
title: RAG Evaluation Metrics
language: markdown
tags: [ai-ml, rag, evaluation, metrics]
selection: null
isPinned: false
timestamp: 1781500001045
---
# RAG Evaluation Metrics

RAG evaluation measures both retrieval quality and generation quality. A good RAG system must retrieve relevant context AND produce faithful answers.

## Evaluation Dimensions

| Dimension | Measures | Metrics |
|-----------|----------|---------|
| Retrieval Relevance | How relevant are the retrieved chunks? | MRR, MAP, NDCG, Recall@k |
| Answer Faithfulness | Does the answer stick to the context? | Faithfulness, Hallucination rate |
| Answer Completeness | Does the answer fully address the query? | Coverage, Completeness |
| Answer Relevance | Is the answer on-topic? | Relevance score, Answer Relevancy |
| Context Precision | Are all retrieved chunks necessary? | Context Precision, Noise ratio |

## Retrieval Metrics

| Metric | Description | Range |
|--------|-------------|-------|
| MRR (Mean Reciprocal Rank) | Rank of first relevant result | 0-1 |
| MAP (Mean Average Precision) | Precision at each relevant doc | 0-1 |
| NDCG (Normalized Discounted Cumulative Gain) | Position-weighted relevance | 0-1 |
| Recall@k | How many relevant docs in top-k | 0-1 |
| Hit Rate | Was any relevant doc retrieved? | 0-1 |

## Generation Metrics

### Faithfulness

Does the answer contradict the context?

```python
def check_faithfulness(answer, context):
    # Extract claims from answer
    claims = decompose_to_claims(answer)
    # Check each claim against context
    faithful_claims = 0
    for claim in claims:
        if is_entailed_by(claim, context):
            faithful_claims += 1
    return faithful_claims / len(claims)
```

### Answer Relevancy

Is the answer relevant to the query?

```python
def answer_relevancy(query, answer):
    # Generate synthetic questions from answer
    gen_questions = llm.generate_questions(answer)
    # Measure similarity with original query
    return cosine_similarity(embed(query), embed(gen_questions))
```

### Context Recall

Does the context contain all needed information?

## Evaluation Datasets

| Dataset | Domain | Size | Metrics |
|---------|--------|------|---------|
| KILT | Wikipedia QA | 86K | R-Precision, Recall |
| BEIR | 18 retrieval datasets | Diverse | NDCG@10 |
| RGB | RAG-specific benchmark | 600 | Faithfulness, Relevance |
| ARES | LLM-as-judge RAG eval | Custom | All dimensions |
| RAGAS | RAG Assessment | Synthetic | F1, Precision, Recall |

## RAGAS Framework

RAGAS computes 3 core metrics:

| Metric | What It Measures |
|--------|-----------------|
| Faithfulness | Generated claims grounded in context |
| Answer Relevance | Answer addresses the question |
| Context Precision | Retrieved chunks are relevant |

```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_precision

result = evaluate(
    dataset=dataset,
    metrics=[faithfulness, answer_relevancy, context_precision]
)
```

## LLM as Judge

Use a strong LLM to evaluate RAG outputs:

```python
eval_prompt = """
Evaluate if the ANSWER is faithful to the CONTEXT.
Score 1-5:
1 = Major hallucination
3 = Minor unsupported claims
5 = Fully faithful, all claims supported

CONTEXT: {context}
ANSWER: {answer}
Score:
"""
```

## A/B Testing in Production

| Metric | How | What It Reveals |
|--------|-----|-----------------|
| User feedback | Thumbs up/down | Real-world satisfaction |
| Engagement | Follow-up questions | Answer completeness |
| Task success | Did user achieve goal? | End-to-end quality |
| Latency | P50, P99 response time | Retrieval + generation speed |

**Links**: [[Evaluation of RAG Systems]] | [[RAG Architecture]] | [[Advanced RAG Patterns]] | [[Reranking]] | [[Prompt Engineering for RAG]]
