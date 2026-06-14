---
id: tf-008-0000-0000-0000-000000000018
title: Pre-training and Fine-tuning
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781700000018
---
# Pre-training and Fine-tuning

**Links**: [[BERT and Encoder Models]] | [[GPT and Decoder Models]] | [[Transformer Architecture]] | [[RAG vs Fine-tuning]] | [[NLP Pipeline Design]]

## The Paradigm

```
                 Pre-training                     Fine-tuning
     ┌──────────────────────────┐           ┌──────────────────┐
     │  Large unlabeled corpus  │           │  Small labeled    │
     │  (billions of tokens)    │           │  dataset          │
     │  Self-supervised loss     │           │  Task-specific    │
     │  General language ability │           │  Adaptation       │
     └─────────────┬────────────┘           └────────┬─────────┘
                   │                                  │
                   ▼                                  ▼
             ┌──────────┐                      ┌──────────┐
             │ Base Model│  ──→ Fine-tune ──→  │Task Model│
             │ (GPT, BERT)                     │ (Classifier)│
             └──────────┘                      └──────────┘
```

## Pre-training Objectives

| Objective | Model | Description |
|-----------|-------|-------------|
| **MLM** | BERT, RoBERTa | Mask 15% tokens, predict them |
| **Autoregressive** | GPT, Llama | Predict next token left-to-right |
| **Prefix LM** | T5, PaLM | Encoder → decoder (span corruption) |
| **RTD** | ELECTRA | Detect replaced tokens |
| **Contrastive** | SimCSE | Pull similar sentences together |

## Fine-tuning Methods

### Full Fine-tuning

Update all model parameters on task data. Best accuracy but expensive.

### Parameter-Efficient Fine-tuning (PEFT)

Update a small subset of parameters:

| Method | Parameters Updated | Storage |
|--------|-------------------|---------|
| **LoRA** | Low-rank matrices | ~1% of original |
| **Adapter** | Small bottleneck layers | ~3-5% |
| **Prefix Tuning** | Virtual tokens | ~0.1% |
| **Prompt Tuning** | Learnable soft prompts | ~0.01% |

### LoRA (Low-Rank Adaptation)

```python
# Original weight (frozen)
W = nn.Linear(768, 768)

# LoRA matrices (trained)
A = nn.Linear(768, 8)    # Down-project
B = nn.Linear(8, 768)    # Up-project

# Forward: h = Wx + BAx
output = W(x) + B(A(x))
```

## Training Pipeline

```
Data Collection → Cleaning → Tokenization → Pre-training → Evaluation → Fine-tuning → Deployment
```

**Next**: [[Tokenization]] — How text becomes numbers