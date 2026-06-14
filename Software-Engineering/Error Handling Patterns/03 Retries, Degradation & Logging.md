---
tags: [error-handling, retry, graceful-degradation, logging]
---

# 03 — Retries, Degradation & Logging

## Retry Patterns

```python
import time
from functools import wraps

def retry(max_attempts=3, delay=1, backoff=2, exceptions=(Exception,)):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            last_exception = None
            for attempt in range(1, max_attempts + 1):
                try:
                    return func(*args, **kwargs)
                except exceptions as e:
                    last_exception = e
                    if attempt == max_attempts:
                        raise
                    wait = delay * (backoff ** (attempt - 1))
                    log.warning(f"Attempt {attempt} failed: {e}, retrying in {wait}s")
                    time.sleep(wait)
            raise last_exception
        return wrapper
    return decorator

@retry(max_attempts=3, delay=1, backoff=2, exceptions=(ConnectionError, TimeoutError))
def fetch_data(url):
    return requests.get(url, timeout=5)
```

| Strategy | When to Use | Backoff |
|----------|-------------|---------|
| **Fixed delay** | Rate-limited APIs | Same wait each time |
| **Exponential backoff** | Network errors, service overload | Multiply by factor each attempt |
| **Jitter** | Avoid thundering herd | Add randomness to backoff |
| **Circuit breaker** | Downstream service down | Stop trying after threshold, probe later |

## Graceful Degradation

```python
def get_user_profile(user_id):
    try:
        return db.fetch_profile(user_id)
    except DatabaseError:
        log.warning("DB unavailable, using cache")
        try:
            return cache.fetch_profile(user_id)
        except CacheError:
            return {"user_id": user_id, "name": "Unknown", "is_limited": True}
```

## Logging Errors

| Level | When | Example |
|-------|------|---------|
| **DEBUG** | Development troubleshooting | "Processing item 42" |
| **INFO** | Normal operations | "Order submitted: 12345" |
| **WARNING** | Unexpected but handled | "Retry attempt 2/3" |
| **ERROR** | Operation failed, app continues | "Payment failed for order 12345" |
| **CRITICAL** | App cannot continue | "Database connection lost" |

```python
import logging
logger = logging.getLogger(__name__)

def process_order(order_id):
    try:
        result = payment_processor.charge(order_id)
        logger.info("Order %s processed: %s", order_id, result.status)
    except PaymentError as e:
        logger.error("Payment failed for order %s: %s", order_id, e, exc_info=True)
        raise
```

**Links**: [[Software-Engineering/Error Handling Patterns/04 Boundaries & Handlers]] | [[Software-Engineering/Error Handling Patterns/05 Null Safety, Defensive & Async]] | [[Software-Engineering/Error Handling Patterns/06 Summary & Best Practices]]
**See also**: [[Debugging Strategies]], [[System Design Fundamentals]]
