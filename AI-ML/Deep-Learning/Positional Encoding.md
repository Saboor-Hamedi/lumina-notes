---
id: tf-004-0000-0000-0000-000000000014
title: Positional Encoding
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781700000014
---
# Positional Encoding

**Links**: [[Self-Attention]] | [[Transformer Architecture]] | [[Multi-Head Attention]] | [[BERT and Encoder Models]] | [[GPT and Decoder Models]]

## Why Positional Encoding?

Self-attention is permutation-invariant — it treats the input as a bag of tokens. Without position information, "cat sat" and "sat cat" would produce the same representation.

## Sinusoidal Encoding (Original Transformer)

Uses sine and cosine functions of different frequencies:

```
PE(pos, 2i)     = sin(pos / 10000^(2i / d_model))
PE(pos, 2i + 1) = cos(pos / 10000^(2i / d_model))
```

| Symbol | Meaning |
|--------|---------|
| pos | Position in sequence (0, 1, 2, ...) |
| i | Dimension index (0, 1, 2, ..., d_model/2) |
| d_model | Embedding dimension |

Properties:
- Each position gets a unique encoding
- Relative positions are linear combinations of each other
- No learned parameters — can handle sequences longer than training data

## Learned Positional Encoding (BERT, GPT)

Treats position IDs as embeddings and learns them during training.

```python
self.position_embeddings = nn.Embedding(max_seq_len, d_model)
positions = torch.arange(seq_len, device=device)
pos_embeds = self.position_embeddings(positions)
output = token_embeds + pos_embeds  # Element-wise add
```

## RoPE (Rotary Position Embedding) — Modern Approach

Used by Llama, Mistral, GPT-NeoX. Applies rotation to query and key vectors based on position.

```
Q_rotated = RoPE(Q, pos)
K_rotated = RoPE(K, pos)

score = Q_rotated · K_rotated  # Naturally decays with distance
```

Benefits:
- Relative position awareness
- Natural decay for distant tokens
- Better length generalization

## ALiBi (Attention with Linear Biases)

Adds a linear bias to attention scores based on distance — used in BLOOM, MPT.

```
score = Q·K + bias_matrix
bias[i][j] = -m × |i - j|   (m = slope per head)
```

## Comparison

| Method | Parameters | Relative | Extrapolation | Used By |
|--------|------------|----------|---------------|---------|
| Sinusoidal | None | Implicit | Good | Transformer |
| Learned | d_model × max_len | None | Poor | BERT, GPT-2 |
| RoPE | None | Explicit | Good | Llama, Mistral |
| ALiBi | None | Explicit | Best | BLOOM, MPT |

**Next**: [[BERT and Encoder Models]] — Understanding transformers