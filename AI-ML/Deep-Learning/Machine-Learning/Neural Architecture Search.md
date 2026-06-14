---
id: a1b2c3d4-1118-4000-8000-000000000118
title: Neural Architecture Search
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001118
---
# Neural Architecture Search

Neural Architecture Search (NAS) automates the design of neural network architectures — finding optimal layer types, connections, and hyperparameters for a given task.

## Why NAS?

| Problem | Solution |
|---------|----------|
| Architecture design requires expertise | Automate the search |
| Manual tuning is slow | Parallel, automated exploration |
| Human bias limits innovation | Discover novel architectures |
| Task-specific design | Optimize for latency, memory, accuracy |

## Search Space

What NAS can search over:

| Component | Options |
|-----------|---------|
| Layer type | Conv, Pool, Attention, LSTM, FFN |
| Layer count | 1-100+ layers |
| Channel size | 32, 64, 128, 256 |
| Kernel size | 1, 3, 5, 7 |
| Activation | ReLU, GELU, SiLU, Swish |
| Skip connections | ResNet-style, DenseNet-style |
| Normalization | BatchNorm, LayerNorm, GroupNorm |
| Attention heads | 4, 8, 12, 16 |
| Expansion factor | MLP expansion (e.g., 4x in transformer) |

## Search Strategies

### 1. Reinforcement Learning

```
Controller (RNN) → Samples architecture → Train → Measure accuracy → Update controller via RL
```

**Pros**: Flexible, any search space.
**Cons**: Expensive (2000+ GPU-days for NASNet).

### 2. Evolutionary Methods

```
Population → Mutate → Train → Select → Repeat
```

**Pros**: Simple, parallelizable, good exploration.
**Cons**: Requires large population, slow.

### 3. Gradient-Based (DARTS)

```
Continuous relaxation of architecture choices → Gradient descent over architecture parameters
```

```python
# DARTS: mix of operations, weighted by α
mixed_op(x) = Σ α_i * op_i(x)
```

**Pros**: Efficient (few GPU-days), differentiable.
**Cons**: Memory intensive, discrete → continuous approximation gap.

### 4. One-Shot / Weight-Sharing

```
SuperNet (all possible architectures) → Train once → Subnetworks inherit weights
```

**Pros**: Very efficient (single training run).
**Cons**: Subnetwork quality doesn't perfectly correlate.

## EfficientNet Example (Found by NAS)

| Model | Params | FLOPs | Top-1 Accuracy |
|-------|--------|-------|----------------|
| EfficientNet-B0 | 5.3M | 0.4B | 77.1% |
| EfficientNet-B4 | 19M | 4.2B | 82.6% |
| EfficientNet-B7 | 66M | 37B | 84.3% |
| ResNet-152 | 60M | 11B | 78.6% |

EfficientNet used NAS to find optimal scaling of width/depth/resolution.

## Hardware-Aware NAS

Optimize for real-world deployment constraints:

```python
def objective(accuracy, latency, memory, power):
    # Multi-objective optimization
    return accuracy * (latency_target / latency) ** α * (memory_target / memory) ** β

# MNASNet: NAS optimized for mobile phones
# ProxylessNAS: Directly search on target hardware
```

## NAS for Transformers (Autoformer, NAS-BERT)

Search transformer architectures:

| Component | Options |
|-----------|---------|
| Number of layers | 6-24 |
| Hidden size | 256-1024 |
| Attention heads | 4-16 |
| FFN intermediate | 1024-4096 |
| Activation | ReLU, GELU |
| Layer norm | Pre/post/root-mean-square |

## Practical Considerations

| Challenge | Mitigation |
|-----------|------------|
| Computational cost | Use weight-sharing or zero-shot proxies |
| Search vs train cost | One-shot approaches, predictor-based |
| Transferability | Fine-tune searched architecture |
| Reproducibility | Fixed seeds, search space restrictions |
| Overfitting to search space | Regularization, validation separation |

## Zero-Shot NAS

Predict architecture quality without training:

| Proxy | What It Measures |
|-------|-----------------|
| Grad norm | Gradient signal flow at initialization |
| SNIP | Connection sensitivity |
| Synaptic flow | Data-dependent gradient flow |
| Jacobian covariance | Expressivity of the architecture |

**Links**: [[Computer Vision]] | [[Transformer Architecture]] | [[Hyperparameter Tuning]] | [[Model Quantization]] | [[Inference Optimization]]
