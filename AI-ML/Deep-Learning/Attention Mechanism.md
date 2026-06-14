---
id: tf-007-0000-0000-0000-000000000017
title: Attention Mechanism
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781700000017
---
# Attention Mechanism

**Links**: [[Self-Attention]] | [[Multi-Head Attention]] | [[Transformer Architecture]] | [[BERT and Encoder Models]] | [[GPT and Decoder Models]] | [[Sequence-to-Sequence Models]]

## The Intuition

Attention tells the model **where to look**. Instead of compressing all information into a fixed-size vector, the model can dynamically focus on relevant parts of the input.

## Types of Attention

### Encoder-Decoder (Cross) Attention

Used in translation: the decoder looks at the encoder's output to find relevant source words.

```
Decoder:  "The cat"
               │
               ▼
Encoder: [Le] [chat] [est] [sur] [le] [tapis]
            ↑              (attends to "chat")
```

### Self-Attention

Each position in a sequence attends to all other positions in the same sequence. Used in BERT, GPT, and all modern transformers.

### Causal (Masked) Self-Attention

Self-attention where each position can only attend to previous positions. Used in autoregressive (decoder-only) models.

## Attention as a Retrieval System

Think of attention as **fuzzy dictionary lookup**:

```
Query:  What am I looking for?
        ↓
        ┌─────────────────┐
        │  Attention       │  ← Keys: Do I match the query?
        │  Computation     │  ← Values: What info do I contribute?
        └─────────────────┘
        ↓
Output: Weighted combination of values
```

## Key Advancements

| Year | Milestone | Impact |
|------|-----------|--------|
| 2014 | Bahdanau Attention | First attention for NMT |
| 2015 | Luong Attention | Simplified scoring |
| 2017 | Transformer (Vaswani) | "Attention is All You Need" |
| 2018 | BERT (Devlin) | Bidirectional pre-training |
| 2019 | GPT-2 (Radford) | Large-scale language modeling |
| 2020 | GPT-3 (Brown) | In-context learning emergent |
| 2022 | ChatGPT | RLHF + instruction tuning |

**Next**: [[Pre-training and Fine-tuning]] — Teaching foundation models