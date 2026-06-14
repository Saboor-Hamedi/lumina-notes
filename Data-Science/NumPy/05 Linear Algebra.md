---
id: ds-np-0005-0000-0000-000000000005
title: NumPy Linear Algebra
language: markdown
tags: [numpy, linear-algebra, matrix, svd, eig]
selection: null
isPinned: false
timestamp: 1781700000605
---
# NumPy Linear Algebra

**Links**: [[01 Arrays]] | [[04 Universal Functions]] | [[06 Random]] | [[_MOC]]

## Matrix Operations

```python
import numpy as np

A = np.array([[1, 2], [3, 4]])
B = np.array([[5, 6], [7, 8]])

# Dot product (matrix multiplication)
np.dot(A, B)                     # [[19, 22], [43, 50]]
A @ B                            # Same, Python 3.5+ operator

# Element-wise
A * B                            # [[5, 12], [21, 32]]

# Transpose
A.T
A.transpose()
```

## Matrix Properties

```python
# Determinant
np.linalg.det(A)

# Inverse
np.linalg.inv(A)

# Trace (sum of diagonal)
np.trace(A)

# Rank
np.linalg.matrix_rank(A)

# Norms
np.linalg.norm(A)                # Frobenius norm
np.linalg.norm(A, ord=1)        # L1 norm
np.linalg.norm(A, ord=np.inf)   # Infinity norm
```

## Solve Linear Systems

```python
# Solve Ax = b
A = np.array([[3, 1], [1, 2]])
b = np.array([9, 8])
x = np.linalg.solve(A, b)        # [2, 3]

# Least squares (no exact solution)
A = np.array([[1, 1], [1, 2], [1, 3]])
b = np.array([1, 2, 3])
x, residuals, rank, sv = np.linalg.lstsq(A, b, rcond=None)
```

## Eigenvalues and Eigenvectors

```python
A = np.array([[2, 1], [1, 2]])

eigenvalues, eigenvectors = np.linalg.eig(A)
# eigenvalues:  [3., 1.]
# eigenvectors: [[0.707, -0.707],
#                [0.707,  0.707]]

# For symmetric matrices (faster, guarantees real)
eigenvalues, eigenvectors = np.linalg.eigh(A)
```

## SVD

```python
A = np.random.randn(5, 3)
U, s, Vt = np.linalg.svd(A, full_matrices=False)

# Reconstruct
Sigma = np.diag(s)
A_reconstructed = U @ Sigma @ Vt

# Low-rank approximation (k=2)
k = 2
A_approx = U[:, :k] @ np.diag(s[:k]) @ Vt[:k, :]
```

## Common Applications

```python
# PCA via SVD
def pca(X, n_components=2):
    X_centered = X - X.mean(axis=0)
    U, s, Vt = np.linalg.svd(X_centered, full_matrices=False)
    return X_centered @ Vt[:n_components].T

# Covariance matrix
np.cov(X, rowvar=False)

# Correlation matrix
np.corrcoef(X, rowvar=False)

# Outer product
np.outer(a, b)

# Kronecker product
np.kron(A, B)
```
