---
id: nlp-014-0000-0000-0000-000000000022
title: Prompt Engineering
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781700000022
---
# Prompt Engineering

**Links**: [[Prompt Engineering for RAG]] | [[GPT and Decoder Models]] | [[LLM Agents Framework]] | [[Pre-training and Fine-tuning]] | [[RAG Architecture]]

## What is Prompt Engineering?

Prompt engineering is the practice of designing inputs (prompts) to elicit desired outputs from language models. It's how we communicate intent to LLMs.

## Prompt Components

```
┌─────────────────────────────────────────────┐
│  System Prompt: "You are a helpful assistant"│ ← Role/context
├─────────────────────────────────────────────┤
│  Examples (few-shot):                        │ ← Demonstrations
│  Q: What is 2+2?                             │
│  A: 4                                        │
├─────────────────────────────────────────────┤
│  User Query: "What is the capital of France?"│ ← Task
├─────────────────────────────────────────────┤
│  Constraints: "Answer in one sentence."       │ ← Format
└─────────────────────────────────────────────┘
```

## Techniques

| Technique | Description | When |
|-----------|-------------|------|
| **Zero-shot** | No examples, just instruction | Simple tasks |
| **Few-shot** | Provide 2-5 examples in context | Complex or unusual tasks |
| **Chain-of-thought** | "Let's think step by step" | Reasoning, math |
| **Tree-of-thought** | Explore multiple reasoning paths | Complex problem solving |
| **Self-consistency** | Sample multiple outputs, majority vote | Factual accuracy |
| **Role prompting** | "You are an expert Python developer" | Domain expertise |
| **Negative prompting** | "Don't mention..." | Constraint enforcement |

## Chain-of-Thought Examples

```
Q: Roger has 5 tennis balls. He buys 2 more cans of tennis balls.
   Each can has 3 balls. How many does he have now?
A: Let's think step by step.
   1. Roger starts with 5 balls.
   2. He buys 2 cans × 3 balls = 6 balls.
   3. 5 + 6 = 11.
   4. The answer is 11.
```

## Structured Output

```json
{
  "instructions": "Extract the person, organization, and date from the text. Return as JSON.",
  "example": {
    "text": "Alice joined Google in 2020.",
    "output": {
      "person": "Alice",
      "organization": "Google",
      "date": "2020"
    }
  },
  "input": "Bob was hired by Microsoft on March 15, 2023."
}
```

## Best Practices

- Be specific and explicit
- Use delimiters (```, """, ---) to separate input from instructions
- Provide examples when format matters
- Tell the model what to do, not just what not to do
- Iterate: test, analyze, refine

**Next**: [[Tokenization]] — How text becomes numbers