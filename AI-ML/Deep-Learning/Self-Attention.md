---
id: tf-002-0000-0000-0000-000000000012
title: Self-Attention
language: markdown
tags: [ai-ml, deep-learning, attention, transformer]
selection: null
isPinned: false
timestamp: 1781700000012
---
# Self-Attention

**Links**: [[Transformer Architecture]] | [[Multi-Head Attention]] | [[Positional Encoding]] | [[Attention Mechanism]] | [[BERT and Encoder Models]] | [[GPT and Decoder Models]]

## What is Self-Attention?

Self-attention computes a weighted sum of all elements in a sequence, where weights depend on pairwise compatibility. Each token can directly attend to every other token.

## The Attention Equation

```
Attention(Q, K, V) = softmax(Q × K^T / √d_k) × V
```

| Symbol | Name | Shape | Meaning |
|--------|------|-------|---------|
| Q | Queries | N × d_k | What am I looking for? |
| K | Keys | N × d_k | What do I contain? |
| V | Values | N × d_v | What should I output? |
| d_k | Key dimension | scalar | Scaling factor |
| N | Sequence length | scalar | Number of tokens |

## Step-by-Step

### 1. Compute Q, K, V from input X

```
Q = X × W_Q    (learned projection)
K = X × W_K    (learned projection)
V = X × W_V    (learned projection)
```

### 2. Compute attention scores

```
Scores = Q × K^T          Shape: N × N
```

Row i, column j = how much token i should attend to token j.

### 3. Scale and softmax

```
Scores = Scores / √d_k    (prevent softmax saturation)
Weights = softmax(Scores, dim=-1)
```

Each row now sums to 1.

### 4. Weighted sum

```
Output = Weights × V       Shape: N × d_v
```

## Intuition

```
"The cat sat on the mat because it was comfortable."
                                    └── "it" attends to "cat" (high weight)
                                    └── "it" attends to "mat" (medium weight)
                                    └── "it" attends to "sat"  (low weight)
```

## Complexity

| Aspect | Self-Attention | RNN |
|--------|---------------|-----|
| Computation | O(N² × d) | O(N × d²) |
| Path length | O(1) | O(N) |
| Parallel | Yes | No |

The O(N²) cost is why Transformers struggle with very long sequences (mitigated by sparse attention, Longformer, etc.).

**Next**: [[Multi-Head Attention]] — Multiple perspectives