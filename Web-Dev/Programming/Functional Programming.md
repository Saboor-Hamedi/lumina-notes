---
id: a1b2c3d4-0003-4000-8000-000000000003
title: Functional Programming
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781400000003
---
# Functional Programming

Functional programming (FP) treats computation as the evaluation of mathematical functions, avoiding mutable state and side effects.

## Core Concepts

### Pure Functions
Same input always produces same output, no side effects.

```python
# Pure
def add(a, b): return a + b

# Impure (side effect: prints to stdout)
def add_and_log(a, b):
    print(f"adding {a} + {b}")
    return a + b
```

### Immutability
Data is never modified in place; new copies are created.

```python
original = [1, 2, 3]
new_list = [*original, 4]  # [1, 2, 3, 4]
```

### Higher-Order Functions
Functions that take or return other functions.

```python
map(lambda x: x * 2, [1, 2, 3])  # [2, 4, 6]
filter(lambda x: x > 2, [1, 2, 3, 4])  # [3, 4]
reduce(lambda a, b: a + b, [1, 2, 3])  # 6
```

## Languages

- **Haskell**: Purely functional
- **Elixir/Erlang**: Functional + concurrency
- **Clojure**: Lisp on JVM
- **Scala**: OOP + FP hybrid
- **Rust**: FP-inspired features (iterators, pattern matching)

**See also**: [[Programming Language Paradigms]], [[Big O Notation]], [[Software Design Principles]]