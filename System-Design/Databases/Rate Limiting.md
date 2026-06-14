---
id: new-011-0000-0000-0000-000000000011
title: Rate Limiting
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781600000011
---
# Rate Limiting

**Links**: [[Web Security]] | [[REST API Design]] | [[Caching Strategies]] | [[Message Queues]] | [[API Versioning]]

## What is Rate Limiting?

Rate limiting controls how many requests a client can make in a given time window. It prevents abuse, ensures fair usage, and protects backend resources.

## Algorithms

| Algorithm | Behavior | Pros | Cons |
|-----------|----------|------|------|
| **Token Bucket** | Tokens refill at constant rate | Handles bursts | Complex state |
| **Leaky Bucket** | Processes at constant rate | Smooth output | Drops bursts |
| **Fixed Window** | Reset counter at window boundaries | Simple | Burst at boundary |
| **Sliding Window** | Counter over rolling window | Fairer | More state |
| **Sliding Window Log** | Timestamp-based sliding log | Most accurate | Memory heavy |

## Token Bucket Implementation

```python
import time
from collections import defaultdict

class TokenBucket:
    def __init__(self, rate: float, capacity: int):
        self.rate = rate          # tokens per second
        self.capacity = capacity  # max tokens
        self.tokens = capacity
        self.last_refill = time.time()

    def allow_request(self) -> bool:
        now = time.time()
        elapsed = now - self.last_refill
        self.tokens = min(self.capacity, self.tokens + elapsed * self.rate)
        self.last_refill = now

        if self.tokens >= 1:
            self.tokens -= 1
            return True
        return False
```

## HTTP Headers

Standard rate limit response headers:

```
X-RateLimit-Limit: 100         # Max requests per window
X-RateLimit-Remaining: 45      # Remaining in current window
X-RateLimit-Reset: 1700000000  # Unix timestamp when window resets

# When exceeded:
Retry-After: 30                 # Seconds until retry
429 Too Many Requests           # Status code
```

## Proxy-Level (Nginx)

```nginx
http {
    limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;

    server {
        location /api/ {
            limit_req zone=api burst=20 nodelay;
            proxy_pass http://backend;
        }
    }
}
```

**Next**: [[Database Sharding]] — Horizontal scaling