---
id: nlp-019-0000-0000-0000-000000000027
title: RNNs and LSTMs
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781800000005
---
# RNNs and LSTMs

**Links**: [[Transformer Architecture]] | [[Attention Mechanism]] | [[Sequence-to-Sequence Models]] | [[NLP Pipeline Design]] | [[Pre-training and Fine-tuning]]

## Why RNNs?

Before Transformers, RNNs were the standard for sequence data. They process tokens one at a time, maintaining a hidden state.

## The Vanishing Gradient Problem

RNNs struggle with long-range dependencies because gradients diminish exponentially: "The **cat**, which was fluffy... **was** hungry." The model can't connect "cat" to "was" across many words.

## LSTM (Long Short-Term Memory)

LSTMs add a **cell state** and **gates** to control information flow:

| Gate | Function |
|------|----------|
| Forget | What to discard from memory |
| Input | What new info to store |
| Output | What to output |

## GRU (Gated Recurrent Unit)

Simpler than LSTM: reset gate + update gate. Fewer parameters.

## RNN vs Transformer

| Aspect | RNN/LSTM | Transformer |
|--------|----------|-------------|
| Computation | Sequential | Parallel |
| Long-range | Struggles | Excellent |
| Training | Hard (gradients) | Stable |

**Next**: [[CNNs for NLP]] — Convolutional networks for text