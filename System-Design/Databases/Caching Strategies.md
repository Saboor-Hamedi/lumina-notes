---
id: a1b2c3d4-0001-4000-8000-000000000001
title: Caching Strategies
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781400000001
---

**Links**: [[Redis Deep Dive]] | [[Database Indexing Strategies]] | [[Database Engines Compared]] | [[Consistent Hashing]] | [[Rate Limiting]] | [[Stream Processing]]


# Caching Strategies

Caching stores frequently accessed data in fast-access storage to reduce latency and backend load.

## Cache Levels

| Level | Examples | Speed |
|-------|----------|-------|
| L1/L2 CPU | Per-core cache | ~1ns |
| In-memory | Redis, Memcached | ~1ms |
| CDN | CloudFront, Cloudflare | ~10ms |
| Database | Query cache, materialized views | ~10-100ms |

## Caching Patterns

### Cache-Aside (Lazy Loading)
Application checks cache first, then falls back to DB.

```python
def get_user(id):
    user = cache.get(f"user:{id}")
    if not user:
        user = db.query("SELECT * FROM users WHERE id = %s", id)
        cache.set(f"user:{id}", user, ttl=300)
    return user
```

### Write-Through
Data is written to cache and DB simultaneously.

### Write-Behind
Data is written to cache immediately, DB is updated asynchronously.

## Eviction Policies

| Policy | Behavior |
|--------|----------|
| LRU | Evict least recently used |
| LFU | Evict least frequently used |
| FIFO | Evict oldest first |
| TTL | Expire after time-to-live |

**Links**: [[Redis Deep Dive]] | [[HTTP Caching]] | [[CDN Architecture]] | [[Database Engines Compared]] | [[REST API Design]] | [[SQL Query Optimization]] | [[Consistent Hashing]]

**See also**: [[API Gateway Patterns]], [[Microservices Architecture]], [[Architecture Patterns]]