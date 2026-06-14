---
id: tf-006-0000-0000-0000-000000000016
title: GPT and Decoder Models
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781700000016
---
# GPT and Decoder Models

**Links**: [[Transformer Architecture]] | [[Self-Attention]] | [[Multi-Head Attention]] | [[BERT and Encoder Models]] | [[Pre-training and Fine-tuning]] | [[Prompt Engineering for RAG]]

## What is GPT?

GPT (Generative Pre-trained Transformer) uses only the decoder stack with masked self-attention for autoregressive text generation.

## Architecture

```
Input:   [BOS]  The  cat  sat
           ↓     ↓    ↓    ↓
         ┌────────────────────┐
         │  Masked Self-Attn  │ ← Can't look ahead
         │  Feed-Forward      │
         │  (×N layers)       │
         └────────────────────┘
           ↓     ↓    ↓    ↓
Output:   The  cat  sat  on
                       ↑
                 Predicted next token
```

## Causal (Masked) Attention

Each token can only attend to itself and previous tokens:

```
The:      [The]         (attends to: The)
cat:      [The] [cat]   (attends to: The, cat)
sat:      [The] [cat] [sat] (attends to: The, cat, sat)
```

Implemented by masking future positions to -infinity before softmax.

## Pre-training

**Objective**: Predict the next token given previous tokens.

```
Input:  "The cat sat on the"
Target:                  "mat"

Loss: cross-entropy on the predicted distribution
```

## Evolution of GPT Models

| Model | Params | Training Data | Key Innovation |
|-------|--------|---------------|----------------|
| GPT-1 | 117M | BooksCorpus | Transformer decoder |
| GPT-2 | 1.5B | WebText | Zero-shot transfer |
| GPT-3 | 175B | Common Crawl | In-context learning |
| GPT-3.5 | ~175B | Code + text | RLHF, instruction tuning |
| GPT-4 | ~1.8T | Proprietary | Multimodal, reasoning |

## Modern Decoder Architectures

| Model | Architecture | Key Feature |
|-------|-------------|-------------|
| Llama 3 | Decoder + RoPE + SwiGLU | Open-source, strong performance |
| Mistral | Decoder + Sliding Window | Efficient long context |
| DeepSeek | MoE Decoder | Mixture of experts, efficient scaling |
| Claude 3 | Decoder | Constitutional AI |

## Inference

```python
def generate(prompt, max_tokens=100):
    tokens = tokenize(prompt)
    for _ in range(max_tokens):
        logits = model(tokens)
        next_token = sample(logits[-1])
        tokens.append(next_token)
        if next_token == EOS:
            break
    return detokenize(tokens)
```

**Next**: [[Attention Mechanism]] — The key idea behind transformers