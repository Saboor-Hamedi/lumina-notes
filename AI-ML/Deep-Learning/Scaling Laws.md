---
id: a1b2c3d4-1052-4000-8000-000000000052
title: Scaling Laws
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001052
---
# Scaling Laws

Scaling laws describe how model performance improves with increased compute, data, and parameters — and how to allocate resources optimally.

## The Core Finding (Kaplan et al. 2020)

Test loss follows a power law with each of three factors (holding others constant):

```
L ∝ N^(-α_N)  ×  D^(-α_D)  ×  C^(-α_C)

N = parameters, D = data tokens, C = compute FLOPs
α_N ≈ 0.076, α_D ≈ 0.095, α_C ≈ 0.050
```

## Key Insights

1. **Performance scales as power law** — not diminishing returns, but consistent improvement with scale
2. **No overfitting with more data** — bigger models continue to benefit from more tokens
3. **Compute-optimal allocation** — there's a specific ratio of parameters:tokens for best performance

## Chinchilla Scaling (Hoffmann et al. 2022)

DeepMind found that previous scaling was **data-inefficient** — models were too large for their training data.

| Aspect | Kaplan (OpenAI) | Chinchilla (DeepMind) |
|--------|----------------|----------------------|
| Optimal tokens per param | ~1.5 | ~20 |
| Overfitting threshold | Insufficient data | Much higher data needed |
| Recommendation | More params over more data | More data over more params |

**Chinchilla optimal**: For a given compute budget, train a smaller model on more tokens.

```
Compute-optimal: N* ≈ C/(6*20)  (parameters)
                 D* ≈ C/(6*20)*20  (tokens)

Simplified: 20 tokens per parameter
```

## Practical Implications

| Compute Budget | Old (Kaplan) | Chinchilla-Optimal |
|----------------|-------------|-------------------|
| Fixed C | N=optimal, D=1.5N | N=optimal/13, D=20N |
| Example at 1e23 FLOPs | ~50B params, 75B tokens | ~16B params, 320B tokens |

## Chinchilla vs Real-World

Modern models don't strictly follow Chinchilla:

| Model | Params | Tokens | Tokens/Param |
|-------|--------|--------|--------------|
| Llama 1 | 65B | 1.4T | 21.5 → Chinchilla-optimal |
| Llama 3 | 8B | 15T | 1875 → Far beyond Chinchilla |
| GPT-4 | ~1.7T (est) | ~13T (est) | ~7.6 → Under Chinchilla |

Why? Inference cost and downstream performance matter more than pure loss.

## Emergent Abilities

Certain capabilities appear only above a scale threshold:

```
Scale → Few-shot learning, chain-of-thought, instruction following, in-context learning
```

- Linear scaling: Perplexity, loss
- Breakthrough scaling: Math reasoning, code generation

## Beyond Scaling: Data Quality

Recent work shows data quality can substitute for quantity:

| Approach | Key Insight |
|----------|-------------|
| Data filtering | Web data varies 100x in quality |
| Curriculum learning | Order matters for learning speed |
| Deduplication | Removing near-duplicates improves efficiency |

## Scaling Predictions

| Compute Level | Equivalent Models | Expected Capabilities |
|---------------|------------------|----------------------|
| 1e25 FLOPs | GPT-4 class (~1.7T params) | Advanced reasoning, coding |
| 1e26 FLOPs | Next-gen frontier | Near-human expert domains |
| 1e27 FLOPs | 10x GPT-4 | Potentially superhuman at many tasks |

**Links**: [[Pre-training and Fine-tuning]] | [[Transformer Architecture]] | [[Mixture of Experts]] | [[Inference Optimization]] | [[Quantization for LLMs]]
