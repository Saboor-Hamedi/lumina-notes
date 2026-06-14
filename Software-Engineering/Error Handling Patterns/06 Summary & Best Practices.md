---
tags: [error-handling, best-practices, summary]
---

# 06 — Summary & Best Practices

## Choosing an Error Strategy

| Factor | Recommended Approach |
|--------|---------------------|
| Simple script | Basic try/except |
| Library / SDK | Custom exception hierarchy |
| Functional codebase | Result / Either types |
| Public API | Validation + descriptive errors |
| Distributed system | Retry + circuit breaker + graceful degradation |
| Frontend app | Error boundaries + global handlers |

## Quick Reference

```python
# DO: Catch specific exceptions
try: risky()
except ValueError as e: handle(e)

# DON'T: Catch all silently
try: risky()
except: pass

# DO: Chain exceptions
raise ConfigError("message") from original_error

# DO: Use finally for cleanup
try: f = open(path); return f.read()
finally: f.close()

# DO: Distinguish domain vs technical errors
raise InsufficientFundsError("Balance too low")  # Domain
raise DatabaseConnectionError("DB unreachable")  # Technical

# CONSIDER: Railway-oriented for complex flows
validate(data).and_then(sanitize).and_then(save)

# CONSIDER: Retry with exponential backoff for transient failures
@retry(max_attempts=3, backoff=2)
def call_service(): ...
```

## Common Antipatterns

| Antipattern | Why It's Wrong | Fix |
|-------------|---------------|-----|
| **Bare except** | Catches `KeyboardInterrupt`, `SystemExit` | `except Exception:` |
| **Swallowing exceptions** | `except: pass` hides bugs | Log the error, re-raise if needed |
| **Returning error codes** | Easy to ignore | Use exceptions or Result types |
| **Over-catch** | `except Exception` everywhere | Catch specific exceptions only |
| **Generic exceptions** | `raise Exception("fail")` | Create meaningful exception classes |

**Links**: [[Software-Engineering/Error Handling Patterns/01 Strategy & Exceptions]] | [[Software-Engineering/Error Handling Patterns/02 Functional Error Handling]] | [[Software-Engineering/Error Handling Patterns/04 Boundaries & Handlers]]
**See also**: [[Clean Code Principles]], [[Debugging Strategies]]
