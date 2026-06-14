---
id: new-ai-025-0000-0000-0000-000000000025
title: LLMOps and AI Observability
language: markdown
tags: [mlops, monitoring, devops, llm]
isPinned: false
timestamp: 1781900000025
---

# LLMOps and AI Observability

**Links**: [[MLOps]] | [[Model Monitoring in Production]] | [[Monitoring and Observability]] | [[LLM Evaluation and Benchmarks]] | [[RAG Evaluation Metrics]] | [[Inference Optimization]]

**See also**: [[CI CD Pipelines]], [[Prometheus and Monitoring Systems]], [[Logging Best Practices]], [[Distributed Tracing]], [[LLM Safety and Guardrails]]

## Overview

LLMOps extends MLOps to the unique challenges of LLMs: prompt management, token cost tracking, output quality monitoring, and safety evaluation.

## LLM-Specific Monitoring

### Quality Metrics

| Metric | What It Measures | How |
|--------|-----------------|-----|
| **Faithfulness** | Is the output grounded in context? | NLI-based scoring |
| **Relevance** | Is the output on-topic? | Embedding similarity |
| **Hallucination rate** | % of fabricated facts | Fact-checking LLM |
| **Toxicity** | Harmful content | Classifier (e.g., Perspective API) |
| **Bias** | Demographic fairness | Disparate impact analysis |
| **Latency** | Time to first token + total time | p50, p95, p99 |

### Cost & Performance

```python
# Token accounting
class TokenTracker:
    def __init__(self):
        self.prompts = Counter()
        self.completions = Counter()
        self.costs = Counter()

    def log(self, model, prompt_tokens, completion_tokens):
        self.prompts[model] += prompt_tokens
        self.completions[model] += completion_tokens
        # Cost per 1K tokens
        rates = {"gpt-4": (0.03, 0.06), "claude-3": (0.015, 0.075)}
        prompt_cost = prompt_tokens / 1000 * rates[model][0]
        completion_cost = completion_tokens / 1000 * rates[model][1]
        self.costs[model] += prompt_cost + completion_cost

    def report(self):
        return {
            "total_cost": sum(self.costs.values()),
            "total_tokens": sum(self.prompts.values()) + sum(self.completions.values()),
            "avg_prompt_length": mean(self.prompts.values()),
        }
```

### Prompt Drift Detection

```python
# Monitor if prompts change over time
def detect_prompt_drift(recent_prompts, reference_embeddings, threshold=0.1):
    recent_embeddings = embed_batch(recent_prompts)
    drift = cosine_distance(recent_embeddings.mean(0), reference_embeddings.mean(0))
    alert(drift > threshold, f"Prompt drift detected: {drift:.3f}")
```

## Tracing LLM Calls

```python
from openinference.instrumentation import traces

@traces.llm_call
async def generate_answer(query, context):
    # This is automatically traced
    response = await llm.generate(
        system="Answer based on context",
        messages=[{"role": "user", "content": f"Context: {context}\nQuery: {query}"}]
    )
    return response

# Export traces to observability backend
traces.export(
    endpoint="http://jaeger:14268/api/traces",
    service_name="rag-api"
)
```

## Prompt Management

| Component | Purpose | Tools |
|-----------|---------|-------|
| **Prompt Registry** | Version-controlled prompts | Git, LangSmith, Weights & Biases |
| **A/B Testing** | Compare prompt variants | LangSmith, custom |
| **Prompt Caching** | Reduce costs for repeated prompts | Redis, GPTCache |
| **Canary Deployments** | Roll out prompt changes gradually | Feature flags, gradual rollout |

## Guardrails Integration

```python
# Pre-flight checks (before LLM call)
pre_guards = [
    Guard(check_input_tokens < max_tokens, "Input too long"),
    Guard(not contains_pii(input), "PII detected"),
]

# Post-flight checks (after LLM call)
post_guards = [
    Guard(check_hallucination(output, context) < 0.3, "Hallucination"),
    Guard(not contains_toxic(output), "Toxic output"),
]
```

## The LLMOps Stack

```
┌──────────────────────────────────────┐
│         Application Layer            │
│  (RAG, Agents, Chat, Tools)          │
├──────────────────────────────────────┤
│      LLM Orchestration Layer         │
│  (LangChain, LlamaIndex, Semantic    │
│   Kernel, custom agents)             │
├──────────────────────────────────────┤
│      Observability & Evaluation      │
│  (LangSmith, Weights & Biases,       │
│   Arize, Evidently, MLflow)          │
├──────────────────────────────────────┤
│         LLM Infrastructure           │
│  (TGI, vLLM, Triton, Bedrock,        │
│   OpenAI, Anthropic)                 │
├──────────────────────────────────────┤
│      Monitoring & Alerting           │
│  (Prometheus, Grafana, Jaeger,       │
│   Loki, OpenTelemetry)               │
└──────────────────────────────────────┘
```

## Cross-Domain Connections

- [[MLOps]] — foundation LLMOps builds upon
- [[Monitoring and Observability]] — metrics, logs, traces extended to LLMs
- [[Feature Stores for Machine Learning]] — prompt templates as features
- [[CI CD Pipelines]] — automated evaluation in deployment pipelines
- [[LLM Safety and Guardrails]] — safety checks as part of observability
- [[RAG Pipeline Optimization]] — optimizing cost + quality in RAG
- [[Distributed Tracing]] — tracing LLM calls across services
- [[Service Level Objectives]] — SLOs for LLM-powered features
