---
id: a1b2c3d4-1183-4000-8000-000000000183
title: Apache Cassandra Deep Dive
language: markdown
tags: [system-design, databases, cassandra, wide-column]
selection: null
isPinned: false
timestamp: 1781500001183
---

**Links**: [[Cassandra]] | [[Database Engines Compared]] | [[Consistent Hashing]] | [[LSM-Tree Storage Engines]] | [[Database Replication Strategies]] | [[Database Sharding]]


# Apache Cassandra Deep Dive

Apache Cassandra is a distributed, wide-column NoSQL database designed for high availability and linear scalability with no single point of failure.

## Architecture

```
Data Center 1                Data Center 2
┌──────────────────┐    ┌──────────────────┐
│  Rack 1           │    │  Rack 1           │
│  ┌───┐ ┌───┐     │    │  ┌───┐ ┌───┐     │
│  │ N │ │ N │     │    │  │ N │ │ N │     │
│  │ 1 │ │ 2 │     │    │  │ 4 │ │ 5 │     │
│  └───┘ └───┘     │    │  └───┘ └───┘     │
│  Rack 2           │    │  Rack 2           │
│  ┌───┐ ┌───┐     │    │  ┌───┐ ┌───┐     │
│  │ N │ │ N │     │    │  │ N │ │ N │     │
│  │ 3 │ │   │     │    │  │ 6 │ │   │     │
│  └───┘ └───┘     │    │  └───┘ └───┘     │
└──────────────────┘    └──────────────────┘
```

## Data Model

```
Keyspace → Table → Partition → Row → Column

CREATE TABLE users (
    user_id     UUID,
    email       TEXT,
    name        TEXT,
    age         INT,
    created_at  TIMESTAMP,
    PRIMARY KEY (user_id)                    -- Simple primary key
);

CREATE TABLE orders (
    user_id     UUID,
    order_id    UUID,
    amount      DECIMAL,
    status      TEXT,
    created_at  TIMESTAMP,
    PRIMARY KEY (user_id, created_at, order_id)  -- Composite key
    -- Partition key: user_id
    -- Clustering columns: created_at, order_id
);
```

## Partition Key and Clustering

```sql
-- Partition key determines data placement
-- All rows with same partition key are on the same node

-- Clustering columns determine sort order WITHIN a partition
-- Query speed depends on partition key coverage

-- Efficient queries (must include partition key):
SELECT * FROM orders WHERE user_id = 123;
SELECT * FROM orders WHERE user_id = 123 AND created_at > '2026-01-01';

-- Inefficient (no partition key) — will scan ALL nodes:
SELECT * FROM orders WHERE status = 'pending';  -- ALLOW FILTERING needed → slow
```

## Query Patterns

```sql
-- Create table optimized for access pattern
CREATE TABLE orders_by_user (
    user_id     UUID,
    created_at  TIMESTAMP,
    order_id    UUID,
    amount      DECIMAL,
    status      TEXT,
    PRIMARY KEY (user_id, created_at, order_id)
) WITH CLUSTERING ORDER BY (created_at DESC);

-- Query: recent orders for a user (fast — single partition)
SELECT * FROM orders_by_user WHERE user_id = 123 ORDER BY created_at DESC LIMIT 10;

-- Query: orders for a user in date range
SELECT * FROM orders_by_user
WHERE user_id = 123
  AND created_at >= '2026-01-01'
  AND created_at < '2026-07-01';
```

## Denormalization

```sql
-- Cassandra does NOT support JOINs
-- Design tables for YOUR query patterns, not normalized

-- Table 1: Orders by user
CREATE TABLE orders_by_user (
    user_id     UUID,
    order_id    UUID,
    amount      DECIMAL,
    status      TEXT,
    created_at  TIMESTAMP,
    PRIMARY KEY (user_id, created_at, order_id)
);

-- Table 2: Orders by status (for admin dashboard)
CREATE TABLE orders_by_status (
    status      TEXT,
    created_at  TIMESTAMP,
    order_id    UUID,
    user_id     UUID,
    amount      DECIMAL,
    PRIMARY KEY (status, created_at, order_id)
);

-- Application writes to BOTH tables (same data, different partitions)
```

## Consistency Levels

```sql
-- Per-query consistency
SELECT * FROM users WHERE user_id = 123 USING CONSISTENCY QUORUM;

-- Write consistency
INSERT INTO users (user_id, name) VALUES (123, 'Alice')
    USING CONSISTENCY LOCAL_QUORUM;
```

| Level | Description | Nodes Required |
|-------|-------------|---------------|
| ONE | One replica responds | Fastest, weak consistency |
| TWO | Two replicas | Moderate |
| QUORUM | Majority (N/2+1) | Balance |
| ALL | All replicas | Strongest, slowest |
| LOCAL_QUORUM | Quorum in local DC | Cross-DC safe |
| EACH_QUORUM | Quorum in each DC | Strong cross-DC |
| SERIAL | Linearizable for lightweight transactions | Conditional updates |

## Tunable Consistency

```
W + R > RF  = Strong consistency
Example: RF=3, W=2, R=2 → W+R=4 > 3 → strong consistency

W + R <= RF = Eventual consistency
Example: RF=3, W=1, R=1 → W+R=2 < 3 → eventual consistency
```

## Lightweight Transactions (LWT)

```sql
-- Compare-and-set (uses Paxos internally — slower)
INSERT INTO users (user_id, email)
VALUES (123, 'alice@example.com')
IF NOT EXISTS;                       -- Only insert if user_id doesn't exist

UPDATE users SET email = 'new@example.com'
WHERE user_id = 123
IF email = 'alice@example.com';      -- Conditional update
```

## Compaction Strategies

```sql
-- Size-Tiered Compaction (default)
-- Merges SSTables of similar size
CREATE TABLE ... WITH compaction = {
    'class': 'SizeTieredCompactionStrategy',
    'min_threshold': 4,
    'max_threshold': 32
};

-- Leveled Compaction (better read performance)
-- Splits data into levels (L0, L1, L2...)
CREATE TABLE ... WITH compaction = {
    'class': 'LeveledCompactionStrategy',
    'sstable_size_in_mb': 160
};

-- TimeWindowCompactionStrategy (time-series data)
CREATE TABLE ... WITH compaction = {
    'class': 'TimeWindowCompactionStrategy',
    'compaction_window_unit': 'DAYS',
    'compaction_window_size': 1
};
```

## Key Tuning Parameters

```ini
# cassandra.yaml

# Memory
memtable_allocation_type: offheap_objects
memtable_space_in_mb: 8192            # 8GB for memtables
concurrent_reads: 32
concurrent_writes: 96

# Caching
chunk_cache_size_in_mb: 512
key_cache_size_in_mb: 100

# GC
# CMS or G1GC for large heaps (>8GB)
# ZGC for very large heaps (>64GB)

# Streaming
stream_throughput_outbound_megabits_per_sec: 400
```

## Monitoring

```bash
# Key metrics
nodetool status                      # Cluster state
nodetool info                        # Node stats
nodetool cfstats my_keyspace         # Table stats
nodetool tpstats                     # Thread pool stats
nodetool compactionstats             # Compaction progress
nodetool gossipinfo                  # Gossip status
nodetool ring                        # Token ranges

# Metrics to watch:
# Pending compactions
# Read/write latencies (p99)
# Hinted handoff count
# Dropped mutations
# JVM GC pause time
```
