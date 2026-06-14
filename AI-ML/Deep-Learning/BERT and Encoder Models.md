---
id: tf-005-0000-0000-0000-000000000015
title: BERT and Encoder Models
language: markdown
tags: [ai-ml, deep-learning, bert, encoder, transformer]
selection: null
isPinned: false
timestamp: 1781700000015
---
# BERT and Encoder Models

**Links**: [[Transformer Architecture]] | [[Self-Attention]] | [[Multi-Head Attention]] | [[GPT and Decoder Models]] | [[Pre-training and Fine-tuning]] | [[NLP Pipeline Design]]

## What is BERT?

BERT (Bidirectional Encoder Representations from Transformers — Devlin et al., 2019) uses only the encoder stack to build deep bidirectional representations. Unlike autoregressive models, BERT reads text in both directions simultaneously, giving each token access to full left and right context at every layer.

## BERT Architecture (L, H, A)

| Parameter | BERT-base | BERT-large | Description |
|-----------|-----------|------------|-------------|
| L (Layers) | 12 | 24 | Number of stacked Transformer encoder layers |
| H (Hidden size) | 768 | 1024 | Dimension of token embeddings and hidden states |
| A (Attention heads) | 12 | 16 | Parallel attention heads per layer |
| d_ff (FFN size) | 3072 | 4096 | Intermediate feed-forward layer (4× H) |
| Params | 110M | 340M | Total trainable parameters |
| Max seq len | 512 | 512 | Maximum input tokens |
| Training data | BooksCorpus + Wikipedia (3.3B words) | Same | Pre-training corpus |

## Architecture Diagram

```
Input:  [CLS]  The   cat   sat  [SEP]  It   was   nice  [SEP]
          ↓     ↓     ↓     ↓     ↓     ↓     ↓     ↓      ↓
        ┌────────────────────────────────────────────────────────┐
        │                   BERT Encoder (L layers)              │
        │                                                         │
        │  Layer 1:  Multi-Head Self-Attention → Add & LN → FFN  │
        │  Layer 2:  Multi-Head Self-Attention → Add & LN → FFN  │
        │  ...                                                    │
        │  Layer L:  Multi-Head Self-Attention → Add & LN → FFN  │
        └────────────────────────────────────────────────────────┘
          ↓     ↓     ↓     ↓     ↓     ↓     ↓     ↓      ↓
        [CLS]  T₁    T₂    T₃   [SEP]  T₅    T₆    T₇    [SEP]
                                              ↑
                                        [CLS] → classification
                                        Tᵢ    → token-level
```

## Pre-training Tasks

### Masked Language Model (MLM)

Randomly mask 15% of tokens and predict them from bidirectional context:

- 80% of masked tokens → replaced with [MASK]
- 10% → replaced with a random token
- 10% → kept unchanged (biases toward actual token distribution)

```
Input:  The [MASK] sat on the [MASK]
Target:      cat             mat
Loss:   Cross-entropy only on masked positions
```

This forces robust contextual representations since the model cannot simply rely on the [MASK] token.

### Next Sentence Prediction (NSP)

Predict if sentence B follows sentence A in the original document:

```
A: The cat sat on the mat.
B: It was comfortable.              → IsNext (label: 1)
C: The stock market rose today.     → NotNext (label: 0)
```

NSP teaches cross-sentence relationship understanding. Note: RoBERTa (Liu et al., 2019) later showed NSP is unnecessary — removing it matched or exceeded BERT's performance.

## Pre-training vs Fine-tuning

| Phase | Data | Objective | Time | Output |
|-------|------|-----------|------|--------|
| Pre-training | Large unlabeled corpus | MLM + NSP | Days-weeks (8-64 TPUs/GPUs) | Pre-trained checkpoint |
| Fine-tuning | Task-specific labeled data | Task-specific loss (CE, F1, etc.) | Minutes-hours (1-4 GPUs) | Task-adapted model |

## Downstream Task Adaptation

| Task | Input Format | Output Head | Objective |
|------|-------------|-------------|-----------|
| **Text Classification** | [CLS] + text + [SEP] | Linear layer on [CLS] | Softmax over classes |
| **Named Entity Recognition** | Token sequence | Linear per token → CRF | Token-level tags (B-PER, I-ORG, O) |
| **Question Answering** | [CLS] + Q + [SEP] + Passage | Start/end logits per token | Span extraction |
| **Text Similarity (STS)** | [CLS] + A + [SEP] + B | Linear on [CLS] | Regression / class scores |
| **Token Classification** | Token sequence | Linear per token | POS tagging, chunking |
| **Multiple Choice** | [CLS] + ctx + [SEP] + option_i | Linear on [CLS] for each option | Softmax over options |

Fine-tuning adds a randomly initialized classification head and updates all parameters end-to-end with a small learning rate (2e-5 to 5e-5).

## BERT Variants

| Model | Architecture | Params | Key Innovation | Speed vs BERT-base |
|-------|-------------|--------|----------------|-------------------|
| **BERT-base** | L=12, H=768, A=12 | 110M | Original encoder model | 1× (baseline) |
| **BERT-large** | L=24, H=1024, A=16 | 340M | Deeper and wider | 3× slower |
| **RoBERTa** | L=24, H=1024, A=16 | 355M | No NSP, 10× more data, dynamic masking | ~1× |
| **DistilBERT** | L=6, H=768, A=12 | 66M | Knowledge distillation from BERT-base | 2× faster, 97% perf |
| **ALBERT** | L=12, H=768, A=12 | 12M | Parameter sharing + factorized embeddings | 1.5× faster |
| **ELECTRA** | L=24, H=1024, A=16 | 335M | Replaced token detection (discriminator) | Efficient pre-training |
| **DeBERTa-v3** | L=24, H=1024, A=16 | 434M | Disentangled attention + enhanced mask decoder | SOTA on SuperGLUE |
| **MiniLM** | L=6, H=384, A=6 | 22M | Deep self-attention distillation | 3× faster |

**Next**: [[GPT and Decoder Models]] — Generative pre-training
