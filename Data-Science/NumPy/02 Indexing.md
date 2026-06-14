---
id: ds-np-0002-0000-0000-000000000002
title: NumPy Indexing
language: markdown
tags: [numpy, indexing, slicing, boolean]
selection: null
isPinned: false
timestamp: 1781700000602
---
# NumPy Indexing

**Links**: [[01 Arrays]] | [[03 Broadcasting]] | [[04 Universal Functions]] | [[_MOC]]

## Basic Slicing

```python
arr = np.arange(12).reshape(3, 4)
# array([[ 0,  1,  2,  3],
#        [ 4,  5,  6,  7],
#        [ 8,  9, 10, 11]])

arr[0]                       # First row:     [0, 1, 2, 3]
arr[:, 0]                    # First column:  [0, 4, 8]
arr[0:2, :]                  # First 2 rows
arr[:, 1:3]                  # Columns 1-2
arr[::2, ::2]                # Strided: every other row and col
arr[::-1]                    # Reverse rows
```

## Fancy Indexing

```python
# Integer array indexing
arr[[0, 2]]                  # Rows 0 and 2
arr[:, [0, 2]]               # Columns 0 and 2
arr[[0, 2], [0, 3]]         # Elements at (0,0) and (2,3) → [0, 11]

# Using indices arrays
rows = np.array([0, 1])
cols = np.array([2, 3])
arr[rows, cols]              # Elements at (0,2) and (1,3) → [2, 7]

# For all combinations, use ix_
arr[np.ix_([0, 2], [1, 3])]  # Rows 0,2 and columns 1,3 → 2x2 submatrix
```

## Boolean Indexing

```python
arr = np.arange(12).reshape(3, 4)

mask = arr > 5
arr[mask]                    # Flattened array of elements > 5 → [6, 7, 8, 9, 10, 11]

# Modify selected elements
arr[arr > 5] = 0             # Set all elements > 5 to 0

# Multi-condition
arr[(arr > 3) & (arr < 8)]  # AND
arr[(arr < 3) | (arr > 9)]  # OR
arr[~(arr == 5)]             # NOT

# Boolean along axis
arr = np.random.randn(5, 3)
mask = np.any(arr > 0, axis=1)  # Rows where at least one value > 0
arr[mask]

# where
np.where(arr > 0, arr, 0)        # Replace negatives with 0
np.where(arr > 0)                # Indices of True elements
```

## Indexing Performance Notes

| Method | Speed | When to Use |
|--------|-------|-------------|
| Basic slice | Fastest | Regular intervals |
| Boolean mask | Fast | Conditional selection |
| Fancy indexing | Slow (copy) | Irregular access |
| `np.take` | Faster than fancy | Known indices |
| `np.compress` | Like boolean, faster | With boolean array |
