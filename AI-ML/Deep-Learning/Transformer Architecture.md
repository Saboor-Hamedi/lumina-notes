---
id: tf-001-0000-0000-0000-000000000011
title: Transformer Architecture
language: markdown
tags: [ai-ml, deep-learning, transformer, architecture]
selection: null
isPinned: true
timestamp: 1781700000011
---
# Transformer Architecture

**Links**: [[Self-Attention]] | [[Multi-Head Attention]] | [[Positional Encoding]] | [[BERT and Encoder Models]] | [[GPT and Decoder Models]] | [[Attention Mechanism]] | [[Pre-training and Fine-tuning]]

## Overview

The Transformer (Vaswani et al., 2017) replaced RNNs with pure attention mechanisms, enabling parallel computation and better long-range dependencies.

## High-Level Architecture

```
Output
  ↑
┌─────────────┐
│  Decoder     │
│  (Masked     │
│  Self-Attn)  │ ←── Encoder Output (cross-attention)
└──────┬──────┘
       │
┌──────┴──────┐
│  Encoder     │
│  (Self-Attn) │
└──────┬──────┘
       │
    Input
```

## Encoder Block (One Layer)

```
Input
  ↓
┌────────────────┐
│  Multi-Head     │
│  Self-Attention │
└───────┬────────┘
        ↓
    Add & LayerNorm (Residual)
        ↓
┌────────────────┐
│  Feed-Forward   │
│  (MLP)          │
└───────┬────────┘
        ↓
    Add & LayerNorm (Residual)
        ↓
     Output
```

## Key Innovations

| Innovation | Why It Matters |
|------------|---------------|
| **Self-Attention** | Captures all pairwise relationships in one step |
| **Multi-Head** | Attends to different representation subspaces |
| **Positional Encoding** | Injects sequence order information |
| **Residual Connections** | Enables training very deep networks |
| **Layer Normalization** | Stabilizes training |
| **Parallel Processing** | Much faster than sequential RNNs |

## Model Variants

| Model | Encoder | Decoder | Params | Use |
|-------|---------|---------|--------|-----|
| BERT | ✅ | ❌ | 110M-340M | Understanding (classification, NER, QA) |
| GPT | ❌ | ✅ | 125M-1.8T | Generation (text, code, chat) |
| T5 | ✅ | ✅ | 60M-11B | Text-to-text (translation, summarization) |

**Next**: [[Self-Attention]] — The core mechanism