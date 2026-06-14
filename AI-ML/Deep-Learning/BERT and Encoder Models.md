---
id: tf-005-0000-0000-0000-000000000015
title: BERT and Encoder Models
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781700000015
---
# BERT and Encoder Models

**Links**: [[Transformer Architecture]] | [[Self-Attention]] | [[Multi-Head Attention]] | [[GPT and Decoder Models]] | [[Pre-training and Fine-tuning]] | [[NLP Pipeline Design]]

## What is BERT?

BERT (Bidirectional Encoder Representations from Transformers) uses only the encoder stack to build deep bidirectional representations. It reads text in both directions simultaneously.

## Architecture

```
Input:  [CLS]  The  cat  sat  [SEP]  It  was  nice  [SEP]
           ↓     ↓    ↓    ↓     ↓    ↓    ↓    ↓     ↓
         ┌───────────────────────────────────────────┐
         │         BERT Encoder (12/24 layers)        │
         └───────────────────────────────────────────┘
           ↓     ↓    ↓    ↓     ↓    ↓    ↓    ↓     ↓
         [C]    T₁   T₂   T₃   [S]   T₅   T₆   T₇   [S]
```

## Pre-training Objectives

### Masked Language Model (MLM)

Randomly mask 15% of tokens, predict them from context.

```
Input:  The [MASK] sat on the [MASK]
Target:      cat             mat
```

### Next Sentence Prediction (NSP)

Predict if sentence B follows sentence A.

```
A: The cat sat on the mat.
B: It was comfortable.              → IsNext
C: The stock market rose today.     → NotNext
```

## Fine-tuning Tasks

| Task | Input | Output |
|------|-------|--------|
| Classification | [CLS] text | [CLS] → class label |
| NER | Token sequence | Each token → B-PER, I-ORG, O |
| QA | Question + Passage | Start/end span |
| Text similarity | [CLS] text A [SEP] text B | [CLS] → similarity score |

## BERT Family

| Model | Layers | Hidden | Heads | Params |
|-------|--------|--------|-------|--------|
| BERT-base | 12 | 768 | 12 | 110M |
| BERT-large | 24 | 1024 | 16 | 340M |
| RoBERTa | 24 | 1024 | 16 | 355M |
| DistilBERT | 6 | 768 | 12 | 66M |
| ALBERT | 12 | 768 | 12 | 12M |
| DeBERTa-v3 | 24 | 1024 | 16 | 434M |

**Next**: [[GPT and Decoder Models]] — Generative pre-training