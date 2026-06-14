---
tags: [prompting, reasoning, chain-of-thought, pal]
---

# 02 — Reasoning & Logic

## Chain-of-Thought (CoT)

**Core idea**: Force the model to show its work by generating intermediate reasoning steps.

```python
# Zero-shot CoT — simplest, often surprisingly effective
prompt = "Q: {question}\nA: Let's think step by step."

# Few-shot CoT — provide 2-5 reasoning examples
prompt = """Q: A store has 15 apples. It sells 7. Then receives 10. How many?
A: Let's think step by step. 15 - 7 = 8. 8 + 10 = 18. Answer: 18.

Q: {new_question}
A: Let's think step by step."""
```

| Variant | Method | Quality Gain |
|---------|--------|-------------|
| **Zero-shot CoT** | Append "Let's think step by step" | +10-20% |
| **Few-shot CoT** | Provide 2-5 reasoning examples | +20-40% |
| **Auto-CoT** | LLM generates its own CoT examples | +15-25% |
| **Structured CoT** | Enforce numbered steps or JSON | +10-15% |
| **Contrastive CoT** | Show wrong AND correct reasoning | +5-15% |

## Chain-of-Code

**Key insight**: For numerical/symbolic reasoning, have the LLM write and execute code instead of doing arithmetic in natural language. LLMs are far better at generating correct code than at arithmetic.

```
Q: If a train travels 340 miles in 5 hours, what is its average speed?
# Python code to solve:
speed = 340 / 5
print(f"The average speed is {speed} mph")

Execution result: The average speed is 68 mph
Answer: 68 mph
```

## Program-Aided Language Models (PAL)

The LLM generates code for ALL computation. Only natural language is left to the LLM. External runtime executes the code.

| Technique | Reasoning Method | Computation | Best For |
|-----------|-----------------|-------------|----------|
| **CoT** | Natural language step-by-step | LLM does it | General, qualitative |
| **Chain-of-Code** | Code generation + mental | LLM generates, may execute | Math, numerical |
| **PAL** | Full code generation | External Python execution | Complex math, data analysis |

**Links**: [[AI-ML/NLP/Advanced Prompting Techniques/03 Decomposition Techniques]] | [[AI-ML/NLP/Advanced Prompting Techniques/04 Self-Improvement]] | [[AI-ML/NLP/Advanced Prompting Techniques/05 Interaction & Tool-Use]]
**See also**: [[Prompt Engineering]], [[LLM Agents Framework]]
