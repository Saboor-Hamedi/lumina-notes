---
id: a1b2c3d4-1048-4000-8000-000000000048
title: Mixture of Experts
language: markdown
tags: [ai-ml, deep-learning, moe, efficiency]
selection: null
isPinned: false
timestamp: 1781500001048
---
# Mixture of Experts

Mixture of Experts (MoE) is a neural architecture that activates only a subset of parameters per input, enabling larger models without proportional compute cost.

## Motivation

Standard dense models activate all parameters for every input. MoE activates only a fraction — getting more capacity without more FLOPs.

| Model Type | Total Parameters | Active per Token | FLOPs |
|------------|-----------------|------------------|-------|
| Dense | 7B | 7B | 7B |
| MoE | 47B (8 experts) | ~7B (1-2 experts) | ~7B |

## Architecture

```
Input → Router → Expert 1 (FFN) ┐
                 Expert 2 (FFN) ┤── Weighted sum → Output
                 Expert 3 (FFN) ┘
                 ... (8-64 experts)
```

## Router (Gate)

The router computes a probability distribution over experts:

```python
def route(x, num_experts, top_k=2):
    scores = x @ W_router  # shape: [batch, num_experts]
    weights, indices = torch.topk(scores, k=top_k, dim=-1)
    weights = torch.softmax(weights, dim=-1)
    return weights, indices  # selected experts + their weights
```

Router outputs are sparse — most experts get zero weight.

## Load Balancing

Without balancing, the router may always pick the same expert:

| Technique | How It Works |
|-----------|-------------|
| Auxiliary loss | Penalize uneven expert assignment |
| Z-loss | Stabilize router logits |
| Expert choice routing | Let experts pick tokens instead of tokens picking experts |
| Capacity factor | Limit tokens per expert per batch |

Auxiliary loss example:

```python
# Encourage uniform routing across experts
importance = probs.sum(dim=0)  # [num_experts]
loss = coefficient * cv(importance)  # coefficient of variation
```

## Sparse vs Dense MoE

| Aspect | Sparse MoE | Dense MoE |
|--------|------------|-----------|
| Experts active | 1-2 per token | All experts (soft mixture) |
| Compute saved | Significant | None |
| Memory | Full model in memory | Full model |
| Example | Mixtral 8x7B | Soft MoE |

## Notable MoE Models

| Model | Architecture | Total Params | Active Params |
|-------|-------------|--------------|---------------|
| Mixtral 8x7B | 8 experts, top-2 | 47B | 13B |
| GPT-4 (rumored) | 16 experts, top-2 | ~1.7T | ~200B |
| DeepSeek-V2 | Fine-grained MoE | 236B | 21B |
| Qwen1.5-MoE | 8 experts | 14B | 4B |
| Switch Transformer | 2048 experts, top-1 | 1.6T | ~800M |

## Training Challenges

| Challenge | Solution |
|-----------|----------|
| Expert collapse | Load balancing loss |
| Communication overhead | All-to-all for expert sharding |
| Batch size per expert | Capacity factor, expert parallelism |
| Instability | Z-loss, lower learning rate |
| Memory (all experts in RAM) | Expert parallelism (each GPU hosts subset) |

## Inference with MoE

```
Memory: All expert weights must fit in GPU memory (47B total for Mixtral)
Compute: Only 2 experts compute per token (≈13B effective FLOPs)

→ High memory requirement, moderate compute requirement
→ Benefits from expert parallelism across GPUs
```

## Expert Parallelism

```
GPU 0: Expert 0-3
GPU 1: Expert 4-7

Each token routes to GPU with its expert → tokens transferred between GPUs
```

**Links**: [[Transformer Architecture]] | [[GPT and Decoder Models]] | [[Scaling Laws]] | [[Inference Optimization]] | [[Quantization for LLMs]]
