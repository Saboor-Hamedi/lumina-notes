---
id: ds-np-0007-0000-0000-000000000007
title: NumPy Performance
language: markdown
tags: [numpy, performance, strides, memory-layout]
selection: null
isPinned: false
timestamp: 1781700000607
---
# NumPy Performance

**Links**: [[01 Arrays]] | [[03 Broadcasting]] | [[04 Universal Functions]] | [[_MOC]]

## C-Contiguous vs Fortran-Contiguous

```python
arr_c = np.array([[1, 2], [3, 4]], order='C')     # Row-major (default)
arr_f = np.array([[1, 2], [3, 4]], order='F')     # Column-major

arr_c.flags.c_contiguous     # True
arr_f.flags.f_contiguous     # True

# Row-major: rows are stored consecutively in memory
# [row0, row1, row2, ...]
# arr[0] returns a VIEW (fast, contiguous)

# Column-major: columns are stored consecutively
# [col0, col1, col2, ...]
# arr[:, 0] returns a VIEW (fast)

# Wrong layout for your access pattern = slow copies
# Right layout = fast views
```

## Strides

```python
arr = np.array([[1, 2, 3], [4, 5, 6]])
arr.strides                    # (24, 8)  — bytes to move to next row, next col

# Transpose changes strides without moving data
arr.T.strides                  # (8, 24)  — just swapped!

# reshape can change strides
arr.reshape(3, 2).strides      # (16, 8)
```

## Why Vectorization is Fast

```python
import time

n = 10_000_000
arr = np.random.randn(n)

# Python loop: ~2 seconds
start = time.time()
result = [np.tanh(x) for x in arr]
print(f"Python loop: {time.time() - start:.2f}s")

# NumPy ufunc: ~0.05 seconds
start = time.time()
result = np.tanh(arr)
print(f"NumPy ufunc: {time.time() - start:.2f}s")

# Speedup ~40x
```

## Performance Rules

| Rule | Why | Example |
|------|-----|---------|
| Use ufuncs | C loops, no Python overhead | `np.sin(x)` not `[math.sin(v) for v in x]` |
| Use broadcasting | No temporary arrays | `arr - mean` not `arr - np.full_like(arr, mean)` |
| Avoid fancy indexing | Creates copy, no view | Prefer slices over `arr[[0,2,4]]` |
| Use `out` parameter | Reuse memory | `np.add(a, b, out=c)` |
| Use `order='F'` for column ops | If accessing columns often | Arrays loaded from columnar formats |
| Use `np.einsum` for complex products | Optimized path selection | `np.einsum('ij,jk->ik', A, B)` |

```python
# Bad: multiple intermediate arrays
result = np.exp(arr) + np.sin(arr) * np.cos(arr)

# Good: explicit out parameter
temp = np.empty_like(arr)
np.cos(arr, out=temp)
np.sin(arr, out=temp2)  # or reuse
```
