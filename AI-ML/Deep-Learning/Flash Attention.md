---
id: a1b2c3d4-1051-4000-8000-000000000051
title: Flash Attention
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001051
---
# Flash Attention

Flash Attention is an IO-aware exact attention algorithm that is 2-4x faster and uses dramatically less memory than standard attention by tiling and avoiding materialization of the full N×N attention matrix.

## The Problem

Standard attention computes:
1. S = Q × K^T (N×N matrix)
2. P = softmax(S)  (N×N matrix)
3. O = P × V  (N×N × N×d)

Problem: The N×N attention matrix is written to HBM (slow memory) and read back. For N=4096, this is 128MB per head per layer — causing a memory bottleneck.

## IO Complexity

```
Memory Hierarchy (speed):
┌──────────────────┐
│   HBM (GPU VRAM) │ ← Slow (1.5 TB/s)
├──────────────────┤
│   SRAM (On-chip) │ ← Fast (19 TB/s)
└──────────────────┘

Standard Attention:
Q,K,V → HBM → S (HBM) → softmax (HBM) → P (HBM) → O (HBM)  ← many HBM round-trips

Flash Attention:
Q,K,V → HBM → tiles → SRAM → compute → HBM  ← fewer, larger transfers
```

## Key Ideas

1. **Tiling**: Load blocks of Q, K, V into fast SRAM, compute partial attention
2. **Online softmax**: Compute softmax incrementally without the full matrix
3. **Recomputation**: Don't save the N×N attention matrix — recompute in backward pass

## Algorithm Sketch

```python
def flash_attention(Q, K, V, block_size):
    """
    Q, K, V: [N, d] each
    Block-wise computation without materializing N×N matrix
    """
    O = zeros(N, d)
    for q_block in split(Q, block_size):
        # Initialize per-block statistics
        m = full(block_size, -inf)  # row max
        l = zeros(block_size)       # denominator (exp sum)
        o = zeros(block_size, d)    # output
        
        for k_block, v_block in zip(split(K, block_size), split(V, block_size)):
            s = q_block @ k_block.T  # [block, block]
            m_new = max(m, max(s, dim=1))
            p = exp(s - m_new.unsqueeze(1))
            l = exp(m - m_new) * l + sum(p, dim=1)
            o = (exp(m - m_new) * o.unsqueeze(1) + p @ v_block) / l.unsqueeze(1)
            m = m_new
        
        O[q_block.indices] = o
    return O
```

## Memory Savings

| N | Standard (FP16) | Flash Attention |
|---|-----------------|-----------------|
| 1024 | 2 MB | O(N) |
| 4096 | 32 MB | O(N) |
| 16384 | 512 MB | O(N) |
| 65536 | 8 GB | O(N) |

Flash Attention uses O(N) memory vs O(N²) — enabling much longer contexts.

## Speedups

| Setup | Speedup vs Standard |
|-------|-------------------|
| Training (short context) | 2x |
| Training (long context) | 3-4x |
| Inference (long context) | 2-3x |
| Backward pass | 3-5x |

## Flash Attention v2

| Improvement | v1 | v2 |
|-------------|----|----|
| Loop order | K,V outer, Q inner | Q outer, K,V inner |
| Non-causal support | Yes | Optimized |
| Head dimension | Limited | Supports 128+ |
| Speedup | 2x | 2-4x |

## Flash Attention v3

Further optimizations for Hopper GPUs (H100), leveraging:
- Tensor Memory Accelerator (TMA)
- Asynchronous execution
- WGMMA (Warp Group Matrix Multiply-Accumulate)

## When to Use

| Scenario | Benefit |
|----------|---------|
| Long contexts (>4K) | Significant speed and memory savings |
| Training | Faster backward pass |
| High throughput inference | Reduced memory bandwidth |
| Large batch sizes | Lower peak memory per sequence |

**Links**: [[Self-Attention]] | [[Multi-Head Attention]] | [[Transformer Architecture]] | [[KV Cache and Inference]] | [[Inference Optimization]]
