---
id: nlp-012-0000-0000-0000-000000000020
title: Sequence-to-Sequence Models
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781700000020
---
# Sequence-to-Sequence Models

**Links**: [[Transformer Architecture]] | [[Attention Mechanism]] | [[GPT and Decoder Models]] | [[BERT and Encoder Models]] | [[NLP Pipeline Design]]

## What is Seq2Seq?

Sequence-to-sequence models map an input sequence to an output sequence of potentially different length. Used for translation, summarization, and text generation.

## Encoder-Decoder Architecture

```
Input:  "Le chat est sur le tapis"
              │
              ▼
┌──────────────────────────────┐
│         Encoder               │
│   (Understands input)         │
└──────────────┬───────────────┘
               │ Encoder representation
               ▼
┌──────────────────────────────┐
│         Decoder               │
│   (Generates output)          │
└──────────────┬───────────────┘
               │
               ▼
Output: "The cat is on the mat"
```

## Training (Teacher Forcing)

During training, the decoder receives the **correct previous token** as input, not its own prediction:

```
Decoder input:  [BOS]  The   cat   is   on   the
                    ↓     ↓     ↓    ↓    ↓     ↓
Predictions:         The   cat   is   on   the   mat
                    ↑     ↑     ↑    ↑    ↑
Loss computed against: The  cat   is   on   the   mat
```

## Inference (Autoregressive)

During inference, the decoder feeds its own prediction as the next input:

```
Step 1: [BOS] → predicts "The"
Step 2: [BOS] The → predicts "cat"
Step 3: [BOS] The cat → predicts "is"
...
Step N: [BOS] The cat is on the mat → predicts [EOS]
```

## Beam Search

Instead of greedy (always picking the most probable token), beam search keeps K candidates:

```
Step 1: [The 0.7] [A 0.2] [This 0.1]
Step 2: [The cat 0.5] [The dog 0.3] [A cat 0.1]
Step 3: [The cat is 0.4] [The cat sat 0.3] [The dog is 0.1]
```

Beam width of 4-8 is typical.

## Encoder-Decoder Models

| Model | Params | Specialization |
|-------|--------|---------------|
| T5 | 60M-11B | Text-to-text (all NLP tasks) |
| BART | 140M-400M | Denoising autoencoder |
| mT5 | 300M-13B | Multilingual |
| PEGASUS | 568M | Summarization |

## T5: Text-to-Text Framework

T5 casts every NLP task as text-to-text:

```
Input:  "translate English to French: The cat is on the mat"
Output: "Le chat est sur le tapis"

Input:  "summarize: The cat sat on the mat... (long text)"
Output: "A cat sat on a mat."
```

**Next**: [[Tokenization]] — How text becomes numbers