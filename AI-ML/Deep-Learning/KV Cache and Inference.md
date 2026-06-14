---
id: a1b2c3d4-1049-4000-8000-000000000049
title: KV Cache and Inference
language: markdown
tags: [ai-ml, deep-learning, inference, optimization]
selection: null
isPinned: false
timestamp: 1781500001049
---
# KV Cache and Inference

The KV (Key-Value) cache is the primary optimization for autoregressive transformer inference — it avoids recomputing attention keys and values for every new token.

## The Problem

During autoregressive generation, each new token attends to ALL previous tokens:

```
Step 1: [BOS] → compute K,V for BOS → predict "The"
Step 2: [BOS, The] → recompute K,V for BOS? → predict "cat"
Step 3: [BOS, The, cat] → recompute K,V for BOS and The? → predict "sat"
```

Without caching, each step recomputes all previous K,V pairs — O(n²) compute.

## The Solution: KV Cache

Cache K and V tensors from previous steps and reuse them:

```
Step 1: Compute K1, V1 → cache → predict "The"
Step 2: Load K1,V1 from cache. Compute K2,V2 → append to cache → predict "cat"
Step 3: Load K1,K2,V1,V2 from cache. Compute K3,V3 → append → predict "sat"
```

Each step: compute attention against cached K,V → O(n) per step instead of O(n²).

## Memory Cost

| Layer | Cache Size (per token) |
|-------|----------------------|
| Single KV head | 2 × d_head × dtype_size |
| Multi-head (H heads) | 2 × H × d_head × dtype_size |
| L layers | L × above |

**Example**: Llama 70B (64 layers, 8 KV heads, d_head=128, FP16):
- Per token: 64 × 8 × 128 × 2 × 2 = ~262 KB
- 4096 token context: 262 KB × 4096 = ~1 GB

## Multi-Query Attention (MQA)

MQA shares KV heads across query heads, drastically reducing cache:

| Attention Type | KV Heads | Query Heads | Cache Size |
|---------------|----------|-------------|------------|
| MHA (standard) | 32 | 32 | Full |
| MQA | 1 | 32 | 1/32 |
| GQA (grouped) | 8 | 32 | 1/4 |

Llama 2 70B uses GQA with 8 KV heads. This is specifically for reducing the KV cache size.

## Prefill vs Decode

| Phase | What Happens | Compute Bound |
|-------|-------------|---------------|
| Prefill | Process all input tokens at once, fill KV cache | Compute-bound (matrix multiply) |
| Decode | Generate one token at a time, append to KV cache | Memory-bound (cache read) |

## PagedAttention (vLLM)

KV cache is fragmented across sequences. PagedAttention manages it like virtual memory:

```
KV Cache Blocks (fixed-size pages)
┌────┬────┬────┬────┐
│ S1 │ S1 │ S2 │ S3 │  ← Physical blocks
└────┴────┴────┴────┘
       ↕
Logical blocks per sequence
```

Benefits: Near-zero waste, efficient sharing (parallel sampling), copy-on-write.

## KV Cache Optimization Techniques

| Technique | How |
|-----------|-----|
| KV cache quantization | Store in INT4/INT8 instead of FP16 |
| Window attention | Only cache recent tokens |
| Sliding window | Discard old tokens from cache |
| Cache eviction | Smart eviction of less important tokens |
| Prefix caching | Reuse cache for common prefixes |

## Continuous Batching

Batch across decode steps dynamically — when a sequence finishes generating, immediately start a new one:

```
Time → [Seq1][Seq1][Seq2][Seq2][Seq3]  ← fixed batch
       [Seq1][Seq1][Seq2][Seq3][Seq4]  ← continuous (frees slot immediately)
```

**Links**: [[Transformer Architecture]] | [[Self-Attention]] | [[Multi-Head Attention]] | [[Flash Attention]] | [[Inference Optimization]]
