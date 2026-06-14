---
id: tf-003-0000-0000-0000-000000000013
title: Multi-Head Attention
language: markdown
tags: [ai-ml, deep-learning, attention, transformer]
selection: null
isPinned: false
timestamp: 1781700000013
---
# Multi-Head Attention

**Links**: [[Self-Attention]] | [[Transformer Architecture]] | [[Positional Encoding]] | [[BERT and Encoder Models]] | [[GPT and Decoder Models]]

## Why Multiple Heads?

One attention distribution may not capture all relationships. Multiple heads let the model attend to different aspects simultaneously.

```
Head 1: "The cat sat on the mat"
         ↑--- attends to subject-verb relationship

Head 2: "The cat sat on the mat"
               ↑--- attends to spatial relationship

Head 3: "The cat sat on the mat"
                     ↑--- attends to object relationship
```

## How It Works

```
Input (N × d_model)
        ↓
┌──────────────────────────────┐
│ Split into h heads           │
│ Each: d_k = d_model / h      │
│                              │
│ Head 1: Q₁, K₁, V₁ → Attn₁  │
│ Head 2: Q₂, K₂, V₂ → Attn₂  │
│ ...                          │
│ Head h: Qₕ, Kₕ, Vₕ → Attnₕ  │
│                              │
│ Concatenate: [Attn₁; ...; Attnₕ]  │
│ Project: W_O × concat        │
└──────────────────────────────┘
        ↓
Output (N × d_model)
```

## Mathematical Formulation

```
MultiHead(Q, K, V) = Concat(head₁, ..., headₕ) × W_O

where head_i = Attention(Q × W_Q_i, K × W_K_i, V × W_V_i)
```

## Typical Configuration

| Model | d_model | Heads (h) | d_k |
|-------|---------|-----------|-----|
| BERT-base | 768 | 12 | 64 |
| BERT-large | 1024 | 16 | 64 |
| GPT-3 | 12288 | 96 | 128 |
| Llama 3 70B | 8192 | 64 | 128 |

## Benefits

- Each head learns different relationship types (syntax, semantics, position)
- Ensembles over multiple attention patterns
- More stable training
- Efficient parallel computation

**Next**: [[Positional Encoding]] — Giving the model order information