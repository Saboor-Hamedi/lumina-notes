---
tags: [prompting, self-consistency, self-refine, meta-prompting]
---

# 04 — Self-Improvement

## Self-Consistency

Sample multiple reasoning paths from the same prompt (temperature > 0), then aggregate answers by majority vote.

```python
def self_consistency(llm, prompt, n_samples=5, temperature=0.7):
    answers = [llm.generate(prompt, temperature=temperature) for _ in range(n_samples)]
    counter = Counter(answers)
    return counter.most_common(1)[0]
```

| Aggregation | Best For |
|-------------|----------|
| **Majority vote** | Classification, multiple choice |
| **Weighted vote** (by logit score) | Open-ended generation |
| **Marginal** (token probs) | Short-form answers |

- Use 3-5 samples, temperature 0.5-0.7
- Combine with CoT for best results
- Adds 5-15% accuracy on reasoning tasks

## Self-Refine

Generate → Evaluate → Refine in a loop. The LLM critiques its own output and iteratively improves it.

```python
evaluate_prompt = "Identify factual errors, missing elements, clarity issues, format violations."
refine_prompt = "Improve based on feedback. Preserve what's correct, fix what's not."
```

| Task | Iterations | Convergence |
|------|-----------|-------------|
| Creative writing | 2-3 | Slow (subjective) |
| Code generation | 3-5 | Fast (deterministic) |
| Planning | 2-4 | Medium |
| Summarization | 1-2 | Fast |

## Self-Critique / Constitutional AI

The model critiques its own response against a set of rules or principles, then revises to align.

```python
prompt = "CONSTITUTION: 1. Be accurate 2. Be harmless 3. Be unbiased. Now answer. After your answer, evaluate and revise if needed."
```

## Meta-Prompting

Use the LLM to generate prompts. The LLM acts as a prompt engineer, designing the most effective prompt structure.

```python
meta_prompt = "You are a world-class prompt engineer. Given a task, design the optimal prompt."
```

**Links**: [[AI-ML/NLP/Advanced Prompting Techniques/05 Interaction & Tool-Use]] | [[AI-ML/NLP/Advanced Prompting Techniques/02 Reasoning & Logic]] | [[AI-ML/NLP/Advanced Prompting Techniques/06 Structure & Format]]
**See also**: [[Prompt Engineering]], [[LLM Alignment]]
