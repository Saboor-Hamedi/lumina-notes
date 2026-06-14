---
id: ds-np-0004-0000-0000-000000000004
title: NumPy Universal Functions
language: markdown
tags: [numpy, ufunc, aggregation, vectorization]
selection: null
isPinned: false
timestamp: 1781700000604
---
# NumPy Universal Functions (ufuncs)

**Links**: [[01 Arrays]] | [[03 Broadcasting]] | [[05 Linear Algebra]] | [[_MOC]]

## What Are ufuncs?

ufuncs operate element-by-element on arrays with zero Python loops — they're implemented in C.

```python
# Python loop (slow)
result = [np.sin(x) for x in arr]

# ufunc (fast)
result = np.sin(arr)           # C-level loop, vectorized
```

## Math ufuncs

```python
# Trig
np.sin(arr), np.cos(arr), np.tan(arr)
np.arcsin(arr), np.arccos(arr), np.arctan(arr)

# Exponents and logs
np.exp(arr), np.exp2(arr)
np.log(arr), np.log2(arr), np.log10(arr)
np.log1p(arr)                  # log(1 + x) — stable for small x

# Powers
np.sqrt(arr), np.square(arr)
np.power(arr, 2)
np.cbrt(arr)                   # Cube root
```

## Aggregation ufuncs

```python
arr = np.random.randn(100, 5)

arr.sum()                      # Sum all elements
arr.sum(axis=0)                # Sum along columns
arr.min(), arr.max()
arr.mean(), arr.std(), arr.var()
arr.cumsum()                   # Cumulative sum
arr.cumprod()                  # Cumulative product

# NaN-safe versions (skip NaN)
np.nansum(arr), np.nanmean(arr)
np.nanstd(arr), np.nanvar(arr)
np.nanmin(arr), np.nanmax(arr)
```

## np.where

```python
# Conditional selection
np.where(arr > 0, arr, 0)             # Replace negatives with 0
np.where(arr > 0, 'pos', 'neg')        # String mapping (element-wise)

# Find indices
np.where(arr > 0)                      # Tuple of index arrays
np.argwhere(arr > 0)                   # Array of indices
```

## Comparison ufuncs

```python
np.greater(arr, 0)                     # arr > 0
np.less(arr, 0)                        # arr < 0
np.equal(arr, 0)                       # arr == 0
np.isclose(a, b)                       # Floating point equality
np.allclose(a, b)                      # All elements close?

np.any(arr > 0)                        # Any element > 0?
np.all(arr > 0)                        # All elements > 0?
np.isnan(arr)                          # NaN mask
np.isfinite(arr)                       # Finite mask
```

## Vectorization — Always Prefer ufuncs

```python
# BAD: Python loop (slow)
for i in range(len(arr)):
    arr[i] = arr[i] * 2 + 1

# GOOD: ufunc (fast)
arr = arr * 2 + 1

# BAD: Python if-else
result = []
for x in arr:
    if x > 0:
        result.append(np.sqrt(x))
    else:
        result.append(0)

# GOOD: np.where (vectorized)
result = np.where(arr > 0, np.sqrt(arr), 0)
```
