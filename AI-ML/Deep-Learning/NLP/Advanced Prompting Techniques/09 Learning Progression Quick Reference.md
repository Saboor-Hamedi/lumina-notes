---
id: 09 Learning Progression & Quick Reference
title: 09 Learning Progression Quick Reference
language: markdown
tags:
  - prompting
  - learning-progression
  - quick-reference
selection: null
isPinned: false
customIcon: null
timestamp: 1781929146901
---

# 09 — Learning Progression & Quick Reference

## Learning Progression

```mermaid
graph LR
    L1["1. Basic Prompting"] --> L2["2. Zero-shot CoT"]
    L2 --> L3["3. Few-shot CoT"]
    L3 --> L4["4. Self-Consistency"]
    L4 --> L5["5. ReAct / Tool Use"]
    L5 --> L6["6. Decomposition"]
    L6 --> L7["7. Tree-of-Thoughts"]
    L7 --> L8["8. Custom Combinations"]
```

## Always-Use Defaults

```python
# For any reasoning task, start with:
prompt = original_prompt + "\nLet's think step by step."
temperature = 0.3

# If quality matters, add:
final = majority_vote([llm.generate(prompt, temp=0.5) for _ in range(5)])
```

## Per-Task Recommended Config

```python
TASK_CONFIGS = {
    "math": {"technique": "chain_of_code", "temperature": 0.2, "self_consistency": True},
    "creative_writing": {"technique": "self_refine", "temperature": 0.8, "iterations": 2},
    "factual_qa": {"technique": "react", "temperature": 0.1, "tools": ["search"]},
    "code_generation": {"technique": "self_refine + tests", "temperature": 0.3, "iterations": 3},
    "planning": {"technique": "tree_of_thoughts", "temperature": 0.6, "branches": 3},
}
```

**Links**: [[AI-ML/NLP/Advanced Prompting Techniques/01 Taxonomy & Overview]] | [[AI-ML/NLP/Advanced Prompting Techniques/07 Selection Guide]] | [[AI-ML/NLP/Advanced Prompting Techniques/08 Production Prompt & Pitfalls]]
**See also**: [[Prompt Engineering]], [[Prompt Engineering for RAG]]
