---
id: rag-007-0000-0000-0000-000000000007
title: Prompt Engineering for RAG
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781700000007
---
# Prompt Engineering for RAG

**Links**: [[RAG Architecture]] | [[Advanced RAG Patterns]] | [[Evaluation of RAG Systems]] | [[LLM Agents Framework]] | [[Attention Mechanism]]

## Basic RAG Prompt

```
System:
You are a helpful assistant. Use the provided context to answer the question.
If the context doesn't contain enough information, say so.
Do not make up information.

Context:
{context}

Question: {question}
Answer:
```

## Structured with Citations

```
System:
Rules:
1. Answer ONLY using the provided context
2. If insufficient, say "I don't have enough information"
3. Cite sources as [1], [2]
4. Be concise

Context:
[1] {chunk_1}
[2] {chunk_2}

Question: {question}
Answer:
```

## Prompt Strategies

| Strategy | When | Approach |
|----------|------|----------|
| Zero-shot | Simple Q&A | "Answer based on context" |
| Few-shot | Complex format | Include examples |
| Chain-of-thought | Multi-step | "Think step by step" |
| Constrained | Structured output | JSON or XML format |

## Handling Edge Cases

```
- Empty context: "I don't have information about this."
- Partial context: Answer what you can, note gaps.
- Conflicting context: Present both sides, explain conflict.
- Never invent citations or facts.
```

**Next**: [[Advanced RAG Patterns]] — Corrective, agentic, self-RAG