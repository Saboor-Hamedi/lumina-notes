---
id: ds-np-0006-0000-0000-000000000006
title: NumPy Random
language: markdown
tags: [numpy, random, distributions, seed]
selection: null
isPinned: false
timestamp: 1781700000606
---
# NumPy Random

**Links**: [[01 Arrays]] | [[05 Linear Algebra]] | [[_MOC]]

## Seeding

```python
import numpy as np

# Modern API (NumPy 1.17+)
rng = np.random.default_rng(42)           # Recommended
rng.random(5)

# Legacy API
np.random.seed(42)
np.random.rand(5)
```

## Uniform Distributions

```python
rng = np.random.default_rng()

rng.random((3, 4))                         # Uniform [0, 1), shape (3,4)
rng.uniform(0, 10, size=100)              # Uniform [0, 10)
rng.integers(0, 100, size=10)             # Random integers
```

## Normal Distributions

```python
rng.normal(loc=0, scale=1, size=100)      # Standard normal
rng.standard_normal(100)                  # Same, standard

rng.lognormal(mean=0, sigma=1, size=100)  # Log-normal

# Multivariate normal
mean = [0, 0]
cov = [[1, 0.5], [0.5, 1]]
rng.multivariate_normal(mean, cov, size=100)
```

## Other Distributions

```python
rng.binomial(n=10, p=0.5, size=100)       # Binomial
rng.poisson(lam=3, size=100)              # Poisson
rng.exponential(scale=1, size=100)        # Exponential
rng.gamma(shape=2, scale=2, size=100)     # Gamma
rng.beta(a=2, b=5, size=100)              # Beta
rng.chisquare(df=5, size=100)             # Chi-squared
rng.t(df=5, size=100)                     # Student's t
rng.f(dfnum=5, dfden=10, size=100)        # F distribution
rng.weibull(a=1.5, size=100)              # Weibull
rng.laplace(loc=0, scale=1, size=100)     # Laplace
rng.logistic(loc=0, scale=1, size=100)    # Logistic
```

## Sampling from Arrays

```python
data = np.arange(100)

rng.choice(data, size=10)                 # With replacement
rng.choice(data, size=10, replace=False)  # Without replacement
rng.choice(data, size=10, p=weights)      # Custom probabilities
rng.permutation(data)                     # Shuffled copy
rng.shuffle(data)                         # In-place shuffle

# Bootstrap (resample with replacement)
n_bootstrap = 1000
means = [
    rng.choice(data, size=len(data), replace=True).mean()
    for _ in range(n_bootstrap)
]
```

## Reproducibility

```python
# Generate same sequence across runs
rng = np.random.default_rng(42)
a = rng.random(5)       # Always [0.773, 0.153, 0.693, 0.136, 0.658]

# Advance generator (skip N values)
rng = np.random.default_rng(42)
rng.bit_generator.advance(100)  # Skip first 100 values
```

## Distribution Overview

| Distribution | Method | Parameters | Use Case |
|-------------|--------|------------|----------|
| Uniform | `uniform` | low, high | Random sampling |
| Normal | `normal` | loc, scale | Natural phenomena |
| Binomial | `binomial` | n, p | Yes/no trials |
| Poisson | `poisson` | lam | Count data |
| Exponential | `exponential` | scale | Wait times |
| Beta | `beta` | a, b | Probabilities |
| Gamma | `gamma` | shape, scale | Rainfall, insurance |
