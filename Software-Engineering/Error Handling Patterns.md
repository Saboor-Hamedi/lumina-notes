---
id: a1b2c3d4-0006-4000-8000-000000000006
title: Error Handling Patterns
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781400000006
---
# Error Handling Patterns

Robust error handling prevents crashes, aids debugging, and provides clear feedback to users.

## Patterns

### Try-Catch (Imperative)

```python
try:
    result = risky_operation()
except ValueError as e:
    log.error(f"Invalid input: {e}")
    return fallback_value
except Exception as e:
    log.error(f"Unexpected error: {e}")
    raise
finally:
    cleanup()
```

### Result Type (Functional)

```python
from dataclasses import dataclass
from typing import Generic, TypeVar

T = TypeVar('T')
E = TypeVar('E')

@dataclass
class Ok(Generic[T]):
    value: T

@dataclass
class Err(Generic[E]):
    error: E

Result = Ok[T] | Err[E]
```

### Railway-Oriented Programming
Chain operations where errors short-circuit the pipeline.

```python
result = (parse_input(data)
          .then(validate)
          .then(process)
          .then(save))
```

## Best Practices

- Fail fast and loudly in development
- Graceful degradation in production
- Always log errors with context
- Never swallow exceptions silently
- Use custom exception types for your domain

**See also**: [[Unit Testing Guide]], [[Software Design Principles]], [[Functional Programming]]