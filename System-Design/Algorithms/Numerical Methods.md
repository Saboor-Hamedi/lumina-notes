---
id: a1b2c3d4-1094-4000-8000-000000000094
title: Numerical Methods
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001094
---
# Numerical Methods

Numerical methods approximate solutions to mathematical problems that can't be solved analytically — integrals, differential equations, optimization, and linear systems.

## Floating Point Arithmetic

| Standard | Bits | Exponent | Mantissa | Precision |
|----------|------|----------|----------|-----------|
| Float32 | 32 | 8 | 23 | ~7 decimal digits |
| Float64 | 64 | 11 | 52 | ~16 decimal digits |
| BFloat16 | 16 | 8 | 7 | ~3 decimal digits |

**Key insight**: Floating point is not real arithmetic.

```python
0.1 + 0.2  # 0.30000000000000004 (not 0.3!)
1e16 + 1 - 1e16  # 0.0 (catastrophic cancellation)
```

## Error Types

| Error | Source | Example |
|-------|--------|---------|
| Roundoff | Finite precision | `0.1 + 0.2 ≠ 0.3` |
| Truncation | Approximating infinite process | Taylor series cutoff |
| Discretization | Sampling continuous function | Finite difference |
| Condition | Problem sensitivity to input | Nearly singular matrix |

## Solving Linear Systems

```python
import numpy as np

# Direct: Gaussian elimination (O(n³))
A = np.array([[2, 1], [1, 3]])
b = np.array([4, 5])
x = np.linalg.solve(A, b)  # [1.4, 1.2]

# Iterative: Conjugate Gradient (for large sparse)
from scipy.sparse.linalg import cg
x, info = cg(A_sparse, b, tol=1e-6)

# Condition number — sensitivity to errors
cond = np.linalg.cond(A)  >> 1 means ill-conditioned
```

## Numerical Integration

```python
# Riemann sum (low accuracy)
def riemann(f, a, b, n=1000):
    h = (b - a) / n
    return sum(f(a + i * h) for i in range(n)) * h

# Simpson's rule (higher accuracy)
def simpson(f, a, b, n=1000):
    h = (b - a) / n
    x = [a + i * h for i in range(n + 1)]
    y = [f(xi) for xi in x]
    return h/3 * (y[0] + y[-1] + 4*sum(y[1:-1:2]) + 2*sum(y[2:-1:2]))

# Adaptive quadrature (Scipy)
from scipy.integrate import quad
result, error = quad(np.exp, 0, 1)
```

## Root Finding

```python
# Newton's method (fast, needs derivative)
def newton(f, df, x0, tol=1e-10):
    x = x0
    while abs(f(x)) > tol:
        x -= f(x) / df(x)
    return x

# Bisection (slow, guaranteed)
def bisect(f, a, b, tol=1e-10):
    while (b - a) > tol:
        c = (a + b) / 2
        if f(a) * f(c) < 0: b = c
        else: a = c
    return (a + b) / 2

# Scipy
from scipy.optimize import root
result = root(lambda x: x**2 - 2, 1.0)  # sqrt(2)
```

## Numerical Differentiation

```python
# Finite differences
def forward_diff(f, x, h=1e-8):
    return (f(x + h) - f(x)) / h

def central_diff(f, x, h=1e-8):
    return (f(x + h) - f(x - h)) / (2 * h)  # O(h²) vs O(h)

# Automatic differentiation (used in ML frameworks)
# Uses chain rule to compute exact derivatives
```

## ODE Solvers

```python
# Euler method
def euler(f, y0, t_span, h=0.01):
    t0, tf = t_span
    t, y = t0, y0
    while t < tf:
        y += h * f(t, y)
        t += h
    return y

# RK4 (Runge-Kutta 4th order) — standard choice
def rk4(f, y0, t_span, h=0.01):
    t0, tf = t_span
    t, y = t0, y0
    while t < tf:
        k1 = h * f(t, y)
        k2 = h * f(t + h/2, y + k1/2)
        k3 = h * f(t + h/2, y + k2/2)
        k4 = h * f(t + h, y + k3)
        y += (k1 + 2*k2 + 2*k3 + k4) / 6
        t += h
    return y
```

## Key Principles

| Principle | Meaning |
|-----------|---------|
| Avoid subtraction of nearly equal numbers | Catastrophic cancellation |
| Prefer stable algorithms | Error doesn't amplify exponentially |
| Scale your matrices | Normalize rows/columns |
| Use vectorized operations | BLAS/LAPACK are optimized |

**Links**: [[Julia]] | [[R for Data Science]] | [[Computer Architecture and Organization]] | [[Algorithm Paradigms]] | [[Performance Profiling]]
