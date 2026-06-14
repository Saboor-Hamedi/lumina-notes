---
id: a1b2c3d4-1053-4000-8000-000000000053
title: Long Context Transformers
language: markdown
tags: [ai-ml, deep-learning, long-context, transformer]
selection: null
isPinned: false
timestamp: 1781500001053
---
# Long Context Transformers

Long context transformers extend attention to handle sequences of 100K+ tokens — overcoming the O(N²) memory and compute costs of standard attention.

## The Challenge

| Context Length | N×N Attention Matrix (FP16) | Time vs Short (4K) |
|----------------|----------------------------|-------------------|
| 4K | 32 MB | 1x |
| 32K | 2 GB | 64x |
| 128K | 32 GB | 1024x |
| 1M | 2 TB | 65536x |

Standard attention becomes intractable quickly.

## Positional Encoding for Long Contexts

### RoPE (Rotary Position Embedding)

RoPE encodes position by rotating query/key vectors:

```python
def apply_rope(x, positions, theta=10000):
    # Rotate pairs of dimensions by position-dependent angle
    freqs = 1.0 / (theta ** (torch.arange(0, d, 2) / d))
    cos = cos(positions.unsqueeze(-1) * freqs)
    sin = sin(positions.unsqueeze(-1) * freqs)
    return x * cos + rotate_half(x) * sin
```

**Key property**: RoPE decays attention between distant tokens — useful for length extrapolation.

### ALiBi (Attention with Linear Biases)

Instead of positional embeddings, ALiBi adds a linear bias to attention scores based on distance:

```python
attention_scores = Q @ K.T + bias_matrix  # bias = -m * |i-j|
```

- **m**: Head-specific slope (geometric sequence)
- **Advantage**: Naturally extrapolates to longer sequences without fine-tuning

## Context Extension Techniques

### Position Interpolation (PI)

Scale position indices to fit within trained range:

```
Train: positions 0-4096
Extend: positions 0-32768 → (0/8, 1/8, ..., 32768/8) → maps to (0-4096)
```

### YaRN (Yet another RoPE extensioN method)

Improves on PI by:
- Scaling attention logits to account for density changes
- Using different ratios for different frequency bands

### NTK-aware interpolation

Neural Tangent Kernel theory suggests high frequencies need less scaling:

```python
# High frequencies (close to original): scale less
# Low frequencies (far apart): scale more
scale = (alpha * orig_freq ** 2 + (1 - alpha) * new_freq ** 2) ** 0.5
```

## Efficient Attention Variants

| Mechanism | Complexity | How It Works |
|-----------|------------|-------------|
| Sliding window | O(N × W) | Each token attends to W neighbors |
| Sparse attention | O(N log N) | Fixed sparse pattern (strided + window) |
| Linear attention | O(N × d²) | Kernel trick (RFA, Performer) |
| Ring attention | O(N² / GPUs) | Distribute across devices |
| Infini-attention | O(N) | Compressive memory with retrieval |

## Models with Long Context

| Model | Max Context | Technique |
|-------|-------------|-----------|
| GPT-4 | 128K (rumored) | Unknown |
| Claude 3 | 200K | Unknown |
| Gemini 1.5 | 1M | MoE + efficient attention |
| Llama 3.1 | 128K | RoPE + Position Interpolation |
| Mixtral 8x22B | 64K | Sliding window + RoPE |
| Mamba (SSM) | 1M+ | State space model (no attention) |

## RAG vs Long Context

| Aspect | RAG | Long Context |
|--------|-----|--------------|
| Cost per query | Low (retrieve small subset) | High (process all tokens) |
| Accuracy | May miss relevant info | Sees everything |
| Latency | Fast retrieval | Slow full processing |
| Use case | Cost-sensitive, bounded knowledge | Full document understanding |

## Practical Usage Tips

```python
# For long context inference:
# 1. Use Flash Attention (reduces memory from O(N²) to O(N))
# 2. Enable sliding window if available
# 3. Use vLLM or TGI for PagedAttention
# 4. Quantize KV cache (INT8)

# For long context training:
# 1. Use sequence parallelism across GPUs
# 2. Ring attention or DeepSpeed Ulysses
# 3. Activation checkpointing
```

**Links**: [[Transformer Architecture]] | [[Flash Attention]] | [[KV Cache and Inference]] | [[Positional Encoding]] | [[RAG Architecture]]
