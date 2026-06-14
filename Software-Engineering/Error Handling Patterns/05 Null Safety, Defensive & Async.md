---
tags: [error-handling, null-safety, defensive-programming, async]
---

# 05 — Null Safety, Defensive & Async

## Null Safety Patterns

| Pattern | How It Works | Example |
|---------|-------------|---------|
| **Option type** | Wraps value in Some/None | `Optional[T]`, `Maybe[T]` |
| **Null Object** | Return no-op object instead of None | Empty list, default config |
| **Maybe monad** | Chain operations that may fail | `.map().map().unwrap_or()` |

```python
# Option type pattern
from dataclasses import dataclass
from typing import Generic, TypeVar

T = TypeVar("T")
@dataclass
class Some(Generic[T]):
    value: T
    def map(self, fn): return Some(fn(self.value))
    def unwrap_or(self, default): return self.value

@dataclass
class Nothing:
    def map(self, fn): return self
    def unwrap_or(self, default): return default

# Usage
def find_user(id: int) -> Some | Nothing:
    user = db.query(id)
    return Some(user) if user else Nothing()

result = find_user(42).map(lambda u: u.email).unwrap_or("unknown@example.com")
```

## Defensive Programming vs Failing Fast

| Approach | Philosophy | Best For |
|----------|-----------|----------|
| **Defensive** | Check everything, handle edge cases | Public APIs, user input |
| **Fail Fast** | Crash on unexpected state | Internal code, development |
| **Design by Contract** | Preconditions + postconditions | Libraries, critical systems |

```python
# Defensive
def process_payment(amount: float) -> bool:
    if amount <= 0:
        log.warning(f"Invalid amount: {amount}")
        return False
    if amount > 10000:
        log.warning(f"Large payment: {amount}, flagging for review")
    # ... process

# Fail Fast
def calculate_total(items: list[Item]) -> float:
    assert len(items) > 0, "Items list cannot be empty"
    assert all(i.price >= 0 for i in items), "Negative price found"
    return sum(i.price * i.quantity for i in items)
```

## Error Handling in Async

```python
import asyncio

async def fetch_all(urls):
    async def safe_fetch(url):
        try:
            return await fetch(url)
        except Exception as e:
            log.error(f"Failed to fetch {url}: {e}")
            return None

    results = await asyncio.gather(
        *[safe_fetch(url) for url in urls],
        return_exceptions=True  # Don't raise, return exceptions
    )
    return [r for r in results if r is not None]

# Timeout pattern
async def fetch_with_timeout(url, timeout=5):
    try:
        return await asyncio.wait_for(fetch(url), timeout=timeout)
    except asyncio.TimeoutError:
        log.warning(f"Timeout fetching {url}")
        return None
```

**Links**: [[Software-Engineering/Error Handling Patterns/04 Boundaries & Handlers]] | [[Software-Engineering/Error Handling Patterns/06 Summary & Best Practices]] | [[Software-Engineering/Error Handling Patterns/02 Functional Error Handling]]
**See also**: [[Async Python]], [[Functional Programming Concepts]]
