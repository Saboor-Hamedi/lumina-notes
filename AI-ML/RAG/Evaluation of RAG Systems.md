---
id: rag-009-0000-0000-0000-000000000009
title: Evaluation of RAG Systems
language: markdown
tags: [ai-ml, rag, evaluation]
selection: null
isPinned: false
timestamp: 1781700000009
---
# Evaluation of RAG Systems

**Links**: [[RAG Architecture]] | [[Reranking]] | [[Advanced RAG Patterns]] | [[Retrieval Strategies]] | [[Prompt Engineering for RAG]]

## Evaluation Framework Overview

RAG evaluation spans three dimensions: **retrieval quality**, **generation quality**, and **end-to-end performance**. Each dimension requires distinct metrics and datasets.

```mermaid
flowchart LR
    Q[Query] --> R[Retriever]
    R --> C[Context]
    C --> G[Generator]
    G --> A[Answer]

    subgraph Retrieval Metrics
        R --> R1[Recall@K]
        R --> R2[Precision@K]
        R --> R3[MRR / NDCG]
    end

    subgraph Generation Metrics
        G --> G1[Faithfulness]
        G --> G2[Answer Relevancy]
        G --> G3[Hallucination Rate]
    end

    subgraph End-to-End
        Q --> E[Overall Quality]
        A --> E
    end
```

## Retrieval Metrics

| Metric | What It Measures | Formula / Definition |
|--------|-----------------|---------------------|
| **Recall@K** | Fraction of relevant docs retrieved | `relevant_retrieved / total_relevant` |
| **Precision@K** | Fraction of retrieved docs that are relevant | `relevant_retrieved / K` |
| **MRR** | Rank of the first relevant result | `1 / rank_first_relevant` |
| **NDCG** | Ranking quality with graded relevance | Discounted cumulative gain over ideal ordering |

```python
def recall_at_k(relevant, retrieved, k):
    return len(set(retrieved[:k]) & set(relevant)) / len(relevant)

def precision_at_k(relevant, retrieved, k):
    return len(set(retrieved[:k]) & set(relevant)) / k

def mrr(relevant, retrieved):
    for i, doc in enumerate(retrieved):
        if doc in relevant:
            return 1.0 / (i + 1)
    return 0.0
```

### Context Precision and Recall

**Context precision** measures how much of the retrieved context is actually useful for answering the query. **Context recall** measures whether all necessary information was retrieved.

| Metric | Definition |
|--------|-----------|
| Context Precision | `useful_chunks / total_retrieved_chunks` |
| Context Recall | `necessary_chunks_retrieved / total_necessary_chunks` |

A common failure mode is high retrieval recall but low context precision — the system retrieves everything including noise, drowning the generator in irrelevant text.

## Generation Metrics

| Metric | What It Measures | Good Score |
|--------|-----------------|-----------|
| **Faithfulness** | Answer grounded in context | > 0.85 |
| **Answer relevancy** | Answer addresses the question | > 0.8 |
| **Context relevance** | Retrieved context is useful | > 0.7 |
| **Hallucination rate** | Facts not in context | < 0.1 |

### Faithfulness / Groundedness

Faithfulness checks whether each claim in the generated answer can be attributed to the retrieved context. Decompose the answer into atomic claims and verify each:

```python
def faithfulness_score(answer, context):
    claims = decompose_into_claims(answer)
    supported = 0
    for claim in claims:
        if any(claim in chunk for chunk in context):
            supported += 1
    return supported / len(claims) if claims else 1.0
```

Common unfaithful patterns:
- **Intrinsic hallucination**: Model adds world knowledge not in context
- **Extrinsic hallucination**: Model invents facts contradicting context
- **Over-specificity**: Answer includes details the context lacks

### Answer Relevance

Measures how well the answer addresses the user's question. Generate reverse questions from the answer and compute cosine similarity with the original query:

```python
def answer_relevancy(question, answer, llm):
    reverse_qs = llm.generate(f"Generate questions for: {answer}")
    embeddings = embed([question] + reverse_qs)
    return cosine_similarity(embeddings[0], embeddings[1:]).mean()
```

## Evaluation Datasets

| Dataset | Focus | Size | Description |
|---------|-------|------|-------------|
| **RGB** (Reference-free Groundedness Benchmark) | Faithfulness | 1K+ | Claims-level groundedness annotations |
| **CRUD** (Create Read Update Delete) | End-to-end QA | 3K+ | Operational knowledge base queries |
| **KILT** (Knowledge Intensive Language Tasks) | Multi-task | 86K | 5 tasks: fact-checking, entity linking, QA, etc. |
| **FRAMES** | Multi-hop reasoning | 1.2K | Questions requiring multiple retrieval steps |
| **BERRI** | Retrieval + generation | 4K | Human-annotated faithfulness + relevance |

```python
# Loading KILT for RAG evaluation
from datasets import load_dataset

kilt = load_dataset("kilt_tasks", "aidayago2")
kilt_sample = kilt["validation"].select(range(100))
```

## RAGAS Framework

RAGAS provides built-in metrics that use LLM-as-judge to score RAG pipelines:

```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_precision, context_recall

results = evaluate(
    dataset=dataset,
    metrics=[faithfulness, answer_relevancy, context_precision, context_recall]
)

# results.to_pandas() for analysis
print(results)
```

## LLM-as-Judge (Automated Evaluation)

Use a strong LLM (GPT-4, Claude) as an evaluator. Design rubrics with multi-dimensional scoring:

```python
def evaluate_faithfulness(question, answer, context):
    prompt = f"""You are evaluating a RAG system. Score 1-5:
- 5: All claims fully supported by context
- 3: Most claims supported, minor extrapolation
- 1: Major hallucination or contradiction

Context: {context}
Answer: {answer}
Score:"""
    return parse_score(llm.generate(prompt))
```

**Best practices:**
- Use chain-of-thought scoring (ask the judge to explain before giving a score)
- Evaluate on 50-200 samples — enough for statistical significance
- Compare LLM-as-judge scores against human annotations to calibrate
- Mitigate position bias by shuffling answer candidates

## Human Evaluation Best Practices

| Practice | Recommendation |
|----------|---------------|
| Scoring scale | 5-point Likert: faithfulness, completeness, helpfulness |
| Annotator agreement | Target Cohen's κ > 0.7 |
| Sample size | Minimum 100 query-answer pairs per system variant |
| Blind evaluation | Hide system identity from annotators |
| Edge cases | Include adversarial queries (ambiguous, multi-hop, out-of-distribution) |

**Next**: [[RAG vs Fine-tuning]] — When to use each
