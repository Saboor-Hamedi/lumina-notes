---
id: a1b2c3d4-1058-4000-8000-000000000058
title: Context Window Strategies
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001058
---
# Context Window Strategies

Context window strategies manage how to fit, use, and optimize information within an LLM's limited attention span — balancing completeness, cost, and quality.

## The Context Window Problem

| Challenge | Impact |
|-----------|--------|
| Fixed token limit | Can't fit all information |
| Linear cost (attention) | Slow, expensive generation |
| Lost-in-the-middle | Model forgets middle tokens |
| Positional bias | Prefers first/last tokens |

## Lost in the Middle (Liu et al. 2023)

When a long context is provided, model accuracy degrades for information in the middle:

```
Accuracy:
High ─── ████████████████████
                 ░░░░░░░░
Low  ─── ░░░░░░░░░░░░░░░░░░░░░░░░
         Head              Tail
         Position in Context
```

**Implication**: Put the most critical information at the start or end of context.

## Context Placement Strategies

| Strategy | Where Important Info Goes | Best For |
|----------|--------------------------|----------|
| Front-loading | Beginning of context | Instructions, system prompts |
| Back-loading | End of context | Latest data, recency bias |
| Sandwich | Critical at both ends | Need attention at both |
| Scattered | Spread throughout | Multi-topic tasks |

## RAG vs Long Context

```
RAG:        [Query + Top-k chunks] → Total: ~2-15K tokens
Long Ctx:   [Entire document]       → Total: ~50-200K tokens
```

| Aspect | RAG | Long Context |
|--------|-----|-------------|
| Cost per query | Lower ($-$$) | Higher ($$-$$$) |
| Accuracy | May miss relevant info | Sees everything but loses middle |
| Latency | Fast retrieval | Slow full processing |
| Best for | Large document collections | Single document deep analysis |

## Sliding Window

Process long documents in overlapping windows:

```python
def sliding_window_qa(document, question, window_size=4000, overlap=500):
    answers = []
    for window in chunk_document(document, window_size, overlap):
        prompt = f"Context: {window}\nQuestion: {question}"
        answers.append(llm.generate(prompt))
    # Aggregate or pick best answer
    return consolidate_answers(answers)
```

## Hierarchical Summarization

```
Chunks → Summaries → Meta-summary → Final answer
```

```
Document: 100K tokens

Level 1: 20 chunks → 20 summaries (500 tokens each) = 10K tokens
Level 2: 4 chunks of summaries → 4 meta-summaries = 1K tokens
Level 3: Combine meta-summaries → final = 500 tokens
```

## Multi-Turn Context Management

| Strategy | What It Does | Tradeoff |
|----------|-------------|----------|
| Full history | Keep all turns | Expensive, long |
| Sliding window | Keep last N turns | Loses early context |
| Summarized history | Keep summaries of old turns | Loses detail |
| Hybrid | Recent: full, old: summary | Best balance |

## Streaming and Chunking

```python
# Map-reduce style processing
def map_reduce(document, question):
    # Map: process each chunk independently
    chunk_results = []
    for chunk in split(document, CHUNK_SIZE):
        chunk_results.append(llm(f"{chunk}\n\n{question}", max_tokens=100))
    # Reduce: synthesize answers
    combined = "\n".join(f"Part {i}: {r}" for i, r in enumerate(chunk_results))
    return llm(f"Summarize these partial answers:\n{combined}")
```

## Token Budget Allocation

```python
def allocate_tokens(budget=8000, system_prompt=500, query=200, num_chunks=5):
    remaining = budget - system_prompt - query
    per_chunk = remaining // num_chunks
    return {
        "system": system_prompt,
        "query": query,
        "chunks": [per_chunk] * num_chunks,
        "total": system_prompt + query + per_chunk * num_chunks
    }
```

## Best Practices

- System prompt first (most important position)
- Query/question last (recency bias)
- Most relevant chunks first (but test both positions)
- Instruct the model explicitly: "Use the EARLIEST context for..." or "Use the LATEST context for..."
- Add token counters and truncation logic
- Test with Pinned/Unpinned examples

**Links**: [[Long Context Transformers]] | [[RAG Architecture]] | [[Chunking Strategies]] | [[Advanced RAG Patterns]] | [[Prompt Engineering]]
