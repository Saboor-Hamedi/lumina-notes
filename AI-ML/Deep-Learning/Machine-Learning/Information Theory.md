---
id: a1b2c3d4-1093-4000-8000-000000000093
title: Information Theory
language: markdown
tags: [ai-ml, machine-learning, information-theory, statistics]
selection: null
isPinned: false
timestamp: 1781500001093
---
# Information Theory

Information theory quantifies information, uncertainty, and redundancy. It underpins compression, communication, and machine learning.

## Core Concepts

| Concept | Definition |
|---------|------------|
| Information | Surprise of an event: I(x) = -log₂ P(x) |
| Entropy | Average information content: H(X) = -Σ P(x) log₂ P(x) |
| Cross-entropy | H(P,Q) = -Σ P(x) log₂ Q(x) — used as ML loss |
| KL Divergence | D_KL(P‖Q) = Σ P(x) log(P(x)/Q(x)) — distance between distributions |
| Mutual Information | I(X;Y) = H(X) - H(X|Y) — shared information between variables |

## Entropy Examples

| Distribution | Entropy (bits) | Meaning |
|-------------|----------------|---------|
| Fair coin | 1.0 | Maximum uncertainty |
| Biased coin (90/10) | 0.47 | Less uncertainty |
| Always heads | 0.0 | No uncertainty |
| Fair 6-sided die | 2.58 | log₂ 6 |

```python
import math

def entropy(probabilities):
    return -sum(p * math.log2(p) for p in probabilities if p > 0)

# Fair coin: -0.5*log2(0.5) - 0.5*log2(0.5) = 1.0
entropy([0.5, 0.5])  # 1.0

# Biased coin: -0.9*log2(0.9) - 0.1*log2(0.1) ≈ 0.47
entropy([0.9, 0.1])  # 0.47
```

## KL Divergence

```python
def kl_divergence(p, q):
    return sum(p[i] * math.log2(p[i] / q[i]) for i in range(len(p)) if p[i] > 0)

# Asymmetric! D_KL(P‖Q) ≠ D_KL(Q‖P)
# Used in: variational inference, model distillation, RL
```

## Mutual Information

```
I(X;Y) = H(X) - H(X|Y) = H(Y) - H(Y|X)

= 0 when X and Y are independent (no shared info)
= H(X) when Y perfectly predicts X
```

## Source Coding Theorem (Shannon)

The minimum average number of bits needed to encode a source is its entropy H.

```
Huffman: optimal prefix code, achieves entropy for known distribution
Arithmetic: can encode fractional bits, closer to entropy
```

## Channel Capacity

Maximum rate at which information can be reliably transmitted over a noisy channel:

```
C = max I(X;Y)
  = B × log₂(1 + S/N)  (AWGN channel — Shannon-Hartley)
```

| Channel | SNR | Capacity |
|---------|-----|----------|
| Telephone line | 30 dB | ~30 kbps |
| Wi-Fi | 20 dB | ~10 Mbps/Hz |
| Fiber optic | 40 dB | ~13 Gbps/Hz |

## Applications in ML

| Application | Information Theory Tool |
|-------------|------------------------|
| Classification loss | Cross-entropy H(P,Q) |
| Variational autoencoders | KL divergence (ELBO) |
| Decision trees | Information gain (mutual information) |
| Feature selection | Mutual information I(X;Y) |
| Model compression | Distillation loss (KL) |
| GANs | Jensen-Shannon divergence |
| Data augmentation | Data processing inequality |

## Data Processing Inequality

If X → Y → Z (Markov chain), then I(X;Z) ≤ I(X;Y).

**Implication**: No amount of post-processing can increase the information about the original data — only preserve or reduce it.

## Entropy Encoding Example

```python
import heapq
from collections import Counter

def huffman_encoding(text):
    freq = Counter(text)
    heap = [[weight, [char, ""]] for char, weight in freq.items()]
    heapq.heapify(heap)

    while len(heap) > 1:
        lo = heapq.heappop(heap)
        hi = heapq.heappop(heap)
        for pair in lo[1:]:
            pair[1] = "0" + pair[1]
        for pair in hi[1:]:
            pair[1] = "1" + pair[1]
        heapq.heappush(heap, [lo[0] + hi[0]] + lo[1:] + hi[1:])

    return sorted(heap[0][1:], key=lambda p: len(p[1]))
```

**Links**: [[Cryptography Basics]] | [[Compression]] | [[Cross-Entropy]] | [[Statistical Methods]] | [[Machine Learning Fundamentals]]
