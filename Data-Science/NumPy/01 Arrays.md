---
id: ds-np-0001-0000-0000-000000000001
title: NumPy Arrays
language: markdown
tags: [numpy, arrays, ndarray, dtypes]
selection: null
isPinned: false
timestamp: 1781700000601
---
# NumPy Arrays

**Links**: [[_MOC]] | [[02 Indexing]] | [[03 Broadcasting]] | [[07 Performance]]

## Creating Arrays

```python
import numpy as np

# From lists
arr = np.array([1, 2, 3])                          # 1D
arr = np.array([[1, 2], [3, 4]])                    # 2D

# Ranges
np.arange(10)                                       # [0, 1, ..., 9]
np.arange(2, 10, 2)                                 # [2, 4, 6, 8]
np.linspace(0, 1, 5)                                # [0.0, 0.25, 0.5, 0.75, 1.0]

# Zeros, ones, eye, empty
np.zeros((3, 4))                                     # 3x4 matrix of zeros
np.ones((2, 3))                                      # 2x3 matrix of ones
np.eye(4)                                            # 4x4 identity matrix
np.empty((3, 3))                                     # Uninitialized values

# Random
np.random.rand(3, 3)                                 # Uniform [0, 1)
np.random.randn(100)                                 # Standard normal
np.random.randint(0, 10, size=(3, 4))                # Random integers
```

## Shapes and Reshape

```python
arr = np.arange(12)

arr.shape                   # (12,)
arr.ndim                    # 1
arr.size                    # 12

arr.reshape(3, 4)           # 3x4 matrix
arr.reshape(2, -1)          # -1 infers dimension → (2, 6)
arr.reshape(2, 2, 3)        # 3D array

# Flatten
arr.ravel()                 # Flatten (returns view when possible)
arr.flatten()               # Flatten (always returns copy)
```

## dtypes

```python
# Specify dtype on creation
arr = np.array([1, 2, 3], dtype=np.float32)
arr = np.arange(10, dtype=np.int64)

# Common dtypes
np.int8, np.int16, np.int32, np.int64
np.uint8, np.uint16, np.uint32, np.uint64
np.float16, np.float32, np.float64
np.complex64, np.complex128
np.bool_
np.object_

# Cast
arr.astype(np.float32)
arr.astype(np.int32)
```

## Copy vs View

```python
# View (shares data — modifying one affects the other)
view = arr.view()
view = arr[:5]              # Slices are views

# Copy (owns its data)
copy = arr.copy()
copy = arr[:5].copy()
```

## Axis Convention

```python
# 2D array: axis 0 = rows, axis 1 = columns
arr = np.array([[1, 2, 3],
                [4, 5, 6]])

arr.sum(axis=0)             # [5, 7, 9]    — column sums
arr.sum(axis=1)             # [6, 15]      — row sums
arr.sum()                   # 21           — all elements
```
