---
id: a1b2c3d4-1181-4000-8000-000000000181
title: Redis Deep Dive
language: markdown
tags: [system-design, databases, redis, key-value]
selection: null
isPinned: false
timestamp: 1781500001181
---
---
id: a1b2c3d4-1181-4000-8000-000000000181
title: Redis Deep Dive
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001181
---

# Redis Deep Dive

**Links**: [[Caching Strategies]] | [[Message Queues]] | [[Consistent Hashing]] | [[Rate Limiting]] | [[Database Engines Compared]] | [[Event Sourcing]]

**See also**: [[Database Replication Strategies]], [[Microservices Architecture]], [[API Gateway Patterns]]

Redis is an in-memory data structure store used as cache, message broker, and database. It supports strings, hashes, lists, sets, sorted sets, streams, and more.

## Data Structures

```bash
# Strings — basic key-value
SET user:123:name "Alice"
GET user:123:name
SET counter 100
INCR counter
INCRBY counter 50

# Hashes — objects
HSET user:123 name "Alice" email "alice@example.com" age 30
HGET user:123 name
HGETALL user:123

# Lists — ordered sequences (message queue)
LPUSH queue:email "user1@example.com"
RPOP queue:email
LLEN queue:email
# Use for: task queues, recent items

# Sets — unique, unordered
SADD team:backend "alice" "bob" "charlie"
SMEMBERS team:backend
SISMEMBER team:backend "alice"
SINTER team:backend team:devops  # Common members

# Sorted Sets — unique, ordered by score
ZADD leaderboard 100 "player1" 85 "player2" 120 "player3"
ZRANGE leaderboard 0 -1 WITHSCORES
ZREVRANGE leaderboard 0 2   # Top 3
ZRANK leaderboard "player1"  # Rank

# Bitmaps — bit-level operations
SETBIT user:active:2026-06-13 123 1  # User 123 active today
GETBIT user:active:2026-06-13 123
BITCOUNT user:active:2026-06-13  # Total active users

# HyperLogLog — approximate cardinality
PFADD unique:visitors "ip1" "ip2" "ip3"
PFCOUNT unique:visitors  # ~3 (approximate)

# Streams — append-only log
XADD orders * customer_id 123 amount 99.99
XREAD COUNT 10 STREAMS orders 0
XREADGROUP GROUP order-processor consumer1 COUNT 1 STREAMS orders >
# Use for: event sourcing, message queues
```

## Caching Patterns

### Cache-Aside

```python
def get_user(user_id):
    # Try cache first
    user = redis.get(f"user:{user_id}")
    if user:
        return json.loads(user)

    # Cache miss — load from database
    user = db.query("SELECT * FROM users WHERE id = %s", [user_id])

    # Store in cache with TTL
    redis.setex(f"user:{user_id}", 3600, json.dumps(user))
    return user
```

### Cache-Through

```python
# Redis as cache + primary for read-heavy workloads
# Write goes to database, then invalidates cache
def update_user(user_id, data):
    db.execute("UPDATE users SET ... WHERE id = %s", [user_id])
    redis.delete(f"user:{user_id}")  # Invalidate cache
```

### Thundering Herd Prevention

```python
import random

def get_expensive_data(key, recompute_func, ttl=3600):
    value = redis.get(key)
    if value:
        return json.loads(value)

    # Lock to prevent multiple recomputes
    lock_key = f"lock:{key}"
    if redis.setnx(lock_key, "1", ex=10):
        try:
            value = recompute_func()
            # Add jitter to TTL to prevent mass expiry
            jitter = random.uniform(0, 0.1)
            redis.setex(key, int(ttl * (1 + jitter)), json.dumps(value))
            return value
        finally:
            redis.delete(lock_key)
    else:
        # Wait for first request to finish
        time.sleep(0.1)
        return get_expensive_data(key, recompute_func, ttl)
```

## Persistence

| Option | Mechanism | Durability | Performance Impact |
|--------|-----------|------------|-------------------|
| RDB (snapshot) | Point-in-time dump | Last snapshot | Low (fork) |
| AOF (append-only) | Every write logged | Very high (fsync) | Moderate |
| RDB + AOF | Both | Best (AOF + RDB base) | Highest |

```bash
# RDB only (less durable, better performance)
save 900 1       # Save if 1 key changed in 900s
save 300 10      # Save if 10 keys changed in 300s
save 60 10000    # Save if 10000 keys changed in 60s

# AOF only
appendonly yes
appendfsync everysec   # Balance: fsync every second

# RDB + AOF (recommended)
appendonly yes
aof-use-rdb-preamble yes
```

## Replication and High Availability

```bash
# Sentinel: automatic failover
# 3 Sentinel nodes monitor Redis primary

# Redis Cluster: sharding + replication
# Minimum 3 masters, each with replica
# Automatic failover, no single point of failure

# Cluster configuration
port 7000
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
appendonly yes
```

## Lua Scripting

```lua
-- Atomic Lua script: transfer between accounts
local balance = redis.call("GET", KEYS[1])
if tonumber(balance) < tonumber(ARGV[1]) then
    return 0  -- Insufficient funds
end
redis.call("DECRBY", KEYS[1], ARGV[1])
redis.call("INCRBY", KEYS[2], ARGV[1])
return 1
```

```bash
EVAL "script" 2 source:account target:account 100
```

## Performance Limits

```bash
# Benchmark
redis-benchmark -h localhost -p 6379 -n 100000 -c 50 -P 10

# Typical results:
# GET: 100,000 ops/sec
# SET: 100,000 ops/sec
# Pipeline (batch 10): 1,000,000 ops/sec

# Single-threaded: one request at a time
# CPU-bound: treat carefully (complex Lua, multi-key operations)

# Memory limits
# 1 million small keys (~50 bytes) ≈ 100MB RAM
# SET key value: ~80 bytes overhead per key
```

## Eviction Policies

```bash
# When maxmemory is reached:
maxmemory 4gb

# Policies:
maxmemory-policy allkeys-lru        # Least recently used
maxmemory-policy allkeys-lfu        # Least frequently used
maxmemory-policy volatile-lru       # Only evict keys with TTL
maxmemory-policy volatile-ttl       # Evict keys with shortest TTL
maxmemory-policy noeviction        # Return errors on write
```
