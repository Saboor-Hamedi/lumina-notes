---
tags: [error-handling, functional, result-type, railway]
---

# 02 — Functional Error Handling

## Result Type

```python
from __future__ import annotations
from dataclasses import dataclass
from typing import Generic, TypeVar, Callable

T = TypeVar("T"); U = TypeVar("U"); E = TypeVar("E"); F = TypeVar("F")

@dataclass
class Ok(Generic[T]):
    value: T
    def map(self, fn: Callable[[T], U]) -> Ok[U] | Err: return Ok(fn(self.value))
    def map_error(self, fn): return self
    def and_then(self, fn): return fn(self.value)
    def unwrap(self) -> T: return self.value
    def unwrap_or(self, default: T) -> T: return self.value
    def is_ok(self) -> bool: return True
    def is_err(self) -> bool: return False

@dataclass
class Err(Generic[E]):
    error: E
    def map(self, fn): return self
    def map_error(self, fn): return Err(fn(self.error))
    def and_then(self, fn): return self
    def unwrap(self): raise RuntimeError(f"unwrap on Err: {self.error}")
    def unwrap_or(self, default: T) -> T: return default
    def is_ok(self) -> bool: return False
```

## Railway-Oriented Programming

```python
def validate(data: dict) -> Ok | Err:
    if "email" not in data:
        return Err("Missing email")
    return Ok(data)

def sanitize(data: dict) -> Ok | Err:
    data["email"] = data["email"].strip().lower()
    return Ok(data)

def save(data: dict) -> Ok | Err:
    try:
        db.insert(data)
        return Ok(data)
    except DBError as e:
        return Err(str(e))

# Railway chain — stops at first Err
result = validate(raw_data).and_then(sanitize).and_then(save)
if result.is_ok():
    print(f"Saved: {result.unwrap()}")
else:
    print(f"Failed: {result.unwrap_or('N/A')}")
```

## Either Monad Pattern

```python
@dataclass
class Either(Generic[E, T]):
    def is_left(self): ...
    def is_right(self): ...
    def fold(self, left_fn, right_fn): ...

@dataclass
class Left(Either[E, T]):
    value: E
    def is_left(self): return True
    def fold(self, left_fn, right_fn): return left_fn(self.value)

@dataclass
class Right(Either[E, T]):
    value: T
    def is_right(self): return True
    def fold(self, left_fn, right_fn): return right_fn(self.value)
```

| Approach | Propagation | Handling | Best For |
|----------|-------------|----------|----------|
| **Exceptions** | Implicit (stack unwinding) | try/except | Imperative code |
| **Result type** | Explicit (return value) | Pattern match | Functional code |
| **Either monad** | Explicit (left=error, right=success) | fold/match | Complex error types |

**Links**: [[Software-Engineering/Error Handling Patterns/03 Retries, Degradation & Logging]] | [[Software-Engineering/Error Handling Patterns/01 Strategy & Exceptions]] | [[Software-Engineering/Error Handling Patterns/06 Summary & Best Practices]]
**See also**: [[Functional Programming Concepts]]
