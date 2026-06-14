---
id: dev-069-0000-0000-0000-000000000058
title: Python Type Hints
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781900000010
---
# Python Type Hints

**Links**: [[Async Python]] | [[Object-Oriented Programming]] | [[Unit Testing Guide]] | [[Python Imports and Modules]] | [[Software Design Principles]]

## What are Type Hints?

Type hints annotate variable types for better IDE support, documentation, and static analysis without affecting runtime.

## Basic Types

```python
name: str = "Alice"
age: int = 30
height: float = 1.75
is_active: bool = True
tags: list[str] = ["python", "typing"]
metadata: dict[str, Any] = {"key": "value"}
```

## Function Annotations

```python
def greet(name: str, age: int = 0) -> str:
    return f"Hello {name}, you are {age}"

def process_items(items: list[str]) -> list[int]:
    return [len(item) for item in items]

def fetch_user(user_id: int) -> User | None:
    return database.get(user_id)
```

## Union and Optional

```python
from typing import Optional, Union

# Optional[str] means str or None
def find_user(email: Optional[str]) -> User | None: ...

# Union[int, float] accepts both
def multiply(a: Union[int, float], b: Union[int, float]) -> float: ...

# Python 3.10+ syntax
def add(a: int | float, b: int | float) -> int | float: ...
```

## Generics

```python
from typing import TypeVar, Generic

T = TypeVar('T')

class Stack(Generic[T]):
    def __init__(self) -> None:
        self.items: list[T] = []

    def push(self, item: T) -> None:
        self.items.append(item)

    def pop(self) -> T:
        return self.items.pop()

stack = Stack[int]()  # Type-safe integer stack
```

## Running mypy

```bash
pip install mypy
mypy src/
```

**Next**: [[Async Python]] — Concurrency with asyncio