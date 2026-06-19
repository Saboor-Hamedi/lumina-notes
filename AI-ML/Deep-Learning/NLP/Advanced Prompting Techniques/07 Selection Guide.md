---
tags: [prompting, selection, comparison]
---

# 07 — Selection Guide

## Decision Tree

```mermaid
graph TD
    Q1["Task type?"] -->|Reasoning/Math| R1
    Q1 -->|Creative/Writing| R2
    Q1 -->|Factual QA| R3
    Q1 -->|Multi-step| R4
    Q1 -->|Code| R5
    R1 -->|Single step| COT[CoT + Self-Consistency]
    R1 -->|Multi-step| CHAIN[Chain-of-Code / PAL]
    R2 -->|Need revision| REFINE[Self-Refine]
    R2 -->|No| COT
    R3 -->|External info| REACT[ReAct / RAG]
    R3 -->|No| COT
    R4 -->|Explore alternatives| TOT[Tree-of-Thoughts]
    R4 -->|No| PLAN[Plan-and-Solve]
    R5 -->|Complex logic| CHAIN
    R5 -->|No| REFINE
```

## Technique Comparison

| Technique | Cost | Accuracy Gain | Best For |
|-----------|------|---------------|----------|
| **Zero-shot CoT** | Low | +10-20% | Default for reasoning |
| **Few-shot CoT** | Medium | +20-40% | Complex reasoning with examples |
| **Chain-of-Code** | Medium | +15-30% | Math, numerical |
| **PAL** | High | +20-35% | Complex computation |
| **Least-to-Most** | Medium | +20-30% | Multi-step problems |
| **Plan-and-Solve** | Medium | +15-25% | Tasks needing planning |
| **Tree-of-Thoughts** | High | +20-40% | Search-like problems |
| **Self-Consistency** | Low-Med | +5-15% | Any reasoning task |
| **Self-Refine** | Medium | +10-25% | Writing, code, planning |
| **ReAct** | Med-High | +20-50% | Tasks needing external info |

```mermaid
quadrantChart
    title Cost vs Quality
    x-axis Low Cost --> High Cost
    y-axis Low Quality --> High Quality
    quadrant-1 High ROI
    quadrant-2 Premium
    quadrant-3 Minimal
    "Zero-shot CoT": [0.2, 0.6]
    "Self-Consistency": [0.5, 0.8]
    "ReAct": [0.65, 0.85]
    "Tree-of-Thoughts": [0.85, 0.85]
    "Basic (no technique)": [0.1, 0.3]
```

**Links**: [[AI-ML/NLP/Advanced Prompting Techniques/08 Production Prompt & Pitfalls]] | [[AI-ML/NLP/Advanced Prompting Techniques/09 Learning Progression & Quick Reference]] | [[AI-ML/NLP/Advanced Prompting Techniques/01 Taxonomy & Overview]]
**See also**: [[Prompt Engineering]], [[LLM Evaluation and Benchmarks]]
