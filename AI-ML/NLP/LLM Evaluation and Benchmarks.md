---
id: a1b2c3d4-1076-4000-8000-000000000076
title: LLM Evaluation and Benchmarks
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001076
---
# LLM Evaluation and Benchmarks

LLM evaluation measures model capabilities across knowledge, reasoning, coding, safety, alignment, and domain-specific tasks.

## Evaluation Categories

| Category | What It Tests | Examples |
|----------|--------------|----------|
| Knowledge | Factual recall | MMLU, TriviaQA |
| Reasoning | Logic, math, multi-step | GSM8K, BigBench, ARC |
| Coding | Code generation, bug fixing | HumanEval, MBPP, SWE-bench |
| Language | Translation, summarization | WMT, ROUGE, BLEU |
| Safety | Harmlessness, bias | AdvBench, TruthfulQA |
| Alignment | Helpfulness, instruction following | MT-Bench, Chatbot Arena |
| Long context | Retrieval over long inputs | LongBench, RULER |
| Agentic | Tool use, planning | SWE-bench, AgentBench |

## Key Benchmarks

| Benchmark | Format | What It Measures | Human Baseline |
|-----------|--------|------------------|----------------|
| MMLU | 57 subjects, 14K questions | Knowledge across domains | ~89% |
| GSM8K | Grade-school math word problems | Mathematical reasoning | ~98% |
| HumanEval | 164 Python coding problems | Code generation | ~96% |
| MT-Bench | 80 multi-turn questions | Chat quality (LLM-as-judge) | N/A |
| AlpacaEval | 805 instructions | Instruction following | N/A |
| BIG-Bench | 204 tasks | Reasoning, logic, creativity | Varies |
| SWE-bench | Real GitHub issues | Software engineering | ~80% |

## Automated Evaluation

### Held-Out Metrics

```python
# Perplexity (lower is better)
# Measures how well model predicts held-out text
from math import exp
neg_log_likelihood = model.evaluate(test_text)
perplexity = exp(neg_log_likelihood)

# Good for tracking training progress
# Weak correlation with downstream task performance
```

### LLM-as-Judge

```python
judge_prompt = """
You are an expert evaluator. Rate the assistant's response on:
1. Helpfulness (1-5): Does it address the user's request?
2. Relevance (1-5): Is the response on-topic?
3. Accuracy (1-5): Are any factual claims correct?
4. Harmlessness (1-5): Is the response safe?

User: {query}
Assistant: {response}

Output JSON with scores and brief reasoning.
"""

def llm_judge(query, response, judge_model="gpt-4o"):
    prompt = judge_prompt.format(query=query, response=response)
    result = judge_model.generate(prompt)
    return json.loads(result)
```

### MT-Bench

Uses multi-turn conversations and LLM-as-judge:

```python
from mt_bench import run_benchmark

scores = run_benchmark(
    model=model,
    num_turns=2,  # each query has 2 conversation turns
    judge_model="gpt-4o",
    categories=["writing", "roleplay", "reasoning", "math",
                "coding", "extraction", "stem", "humanities"]
)
print(f"MT-Bench Score: {scores['average']:.2f}")
```

## Human Evaluation

| Method | Scale | Cost | Reliability |
|--------|-------|------|-------------|
| Chatbot Arena (ELO) | Large | Community | High (crowd) |
| Expert review | Small | High | Very high |
| A/B testing | Medium | Moderate | High (live) |
| User satisfaction | Large | Low | Moderate |

## Task-Specific Evaluation

### Code (HumanEval)

```python
from human_eval.data import read_problems
from human_eval.evaluation import evaluate_functional_correctness

problems = read_problems()
pass_at_k = evaluate_functional_correctness(
    model=model,
    problems=problems,
    k=[1, 10, 100]  # pass@1, pass@10, pass@100
)
```

### Long Context (RULER)

Tests retrieval over various sequence lengths (4K, 8K, 16K, 32K, 64K, 128K):

| Task | What It Tests |
|------|--------------|
| Needle-in-a-haystack | Single fact in long text |
| Multi-query | Multiple facts scattered |
| Variable tracking | Follow variable assignments across text |

## Pitfalls

| Pitfall | Issue | Mitigation |
|---------|-------|------------|
| Benchmark contamination | Model trained on test data | Use held-out, fresh benchmarks |
| Scoring saturation | Models exceed human baseline | Continuously update benchmarks |
| LLM-as-judge bias | Prefers longer, format-matched responses | Control for length, randomize order |
| Single metric focus | Gaming one metric at expense of others | Track diverse metrics |

## Open Leaderboards

| Leaderboard | Metrics | Update |
|-------------|---------|--------|
| LMSys Chatbot Arena | ELO by category | Daily |
| Open LLM Leaderboard (HF) | MMLU, GSM8K, HumanEval | Periodically |
| SWE-bench Leaderboard | % of resolved GitHub issues | Rolling |
| HELM (Stanford) | Multi-metric, multi-scenario | Periodic |

**Links**: [[Evaluation of RAG Systems]] | [[RAG Evaluation Metrics]] | [[LLM Alignment]] | [[Pre-training and Fine-tuning]] | [[Responsible and Ethical AI]]
