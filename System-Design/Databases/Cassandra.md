---
id: a1b2c3d4-1112-4000-8000-000000000112
title: Cassandra
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001112
---
# Cassandra

Apache Cassandra is a distributed, wide-column NoSQL database designed for high availability, linear scalability, and fault tolerance across commodity hardware.

## Key Design

| Feature | Cassandra | Traditional RDBMS |
|---------|-----------|-------------------|
| Architecture | Peer-to-peer (no master) | Master-slave |
| Consistency | Tunable (eventual to strong) | Strong (ACID) |
| Scaling | Linear (add nodes = add capacity) | Vertical (bigger server) |
| Write throughput | Millions/sec | Thousands/sec |
| CAP focus | AP (Availability + Partition Tolerance) | CP |

## Data Model

```
Keyspace (like database)
  └── Table (column family)
        └── Partition Key (determines node)
              └── Clustering Columns (sort within partition)
                    └── Columns (regular data)
```

```sql
CREATE TABLE users (
    user_id UUID,
    created_at TIMESTAMP,
    name TEXT,
    email TEXT,
    PRIMARY KEY ((user_id), created_at)
) WITH CLUSTERING ORDER BY (created_at DESC);
```

**Rule**: Query first, model second. Design tables around your query patterns.

## CQL (Cassandra Query Language)

```sql
-- Keyspace
CREATE KEYSPACE myapp WITH replication = {
    'class': 'NetworkTopologyStrategy',
    'us-east': 3, 'eu-west': 3
};

-- Table
CREATE TABLE orders_by_user (
    user_id UUID,
    order_id TIMEUUID,
    amount DECIMAL,
    status TEXT,
    items LIST<TEXT>,
    PRIMARY KEY ((user_id), order_id)
) WITH CLUSTERING ORDER BY (order_id DESC);

-- Queries (must include partition key)
SELECT * FROM orders_by_user WHERE user_id = ?;
SELECT * FROM orders_by_user WHERE user_id = ? AND order_id > ?;

-- No joins, no GROUP BY, no OR conditions
-- ❌ SELECT * FROM orders WHERE amount > 100;  (no partition key)
```

## Partitioning & Replication

```
Ring topology:
    ┌─── Node A ───┐
    │              │
Node E              Node B
    │              │
    └─── Node D ───┘
    (Node C is virtual)

- Data partitioned by consistent hashing
- Each row replicated to N nodes (replication factor)
- Hinted handoff: if node down, another stores the write
```

## Tunable Consistency

```sql
-- Write consistency
INSERT INTO users (id, name) VALUES (1, 'Alice') USING CONSISTENCY QUORUM;

-- Read consistency
SELECT * FROM users WHERE id = 1 USING CONSISTENCY LOCAL_QUORUM;
```

| Level | Behavior |
|-------|----------|
| ONE | Fastest, weakest (single replica responds) |
| QUORUM | Majority of replicas (R+W > RF for strong consistency) |
| ALL | All replicas (slowest, strongest) |
| LOCAL_QUORUM | Majority in local datacenter |
| EACH_QUORUM | Majority in each datacenter |
| ANY | At least one node (writes always succeed) |

## Compaction

Cassandra merges SSTables in the background:

| Strategy | Best For |
|----------|----------|
| SizeTieredCompactionStrategy (STCS) | Write-heavy workloads (default) |
| LeveledCompactionStrategy (LCS) | Read-heavy, large partitions |
| TimeWindowCompactionStrategy (TWCS) | Time-series data (logs, metrics) |

## Anti-Patterns to Avoid

| Anti-Pattern | Why | Better Approach |
|-------------|-----|----------------|
| Large partitions (> 100 MB) | GC pressure, slow reads | Use clustering keys to limit partition size |
| Batch loading of un-related rows | Performance penalty | Use async writes |
| Secondary indexes on high-cardinality columns | Inefficient lookups | Create a separate table (materialized view) |
| Joins or aggregations | Not supported | Denormalize, pre-compute |
| Lightweight transactions (LWT) in hot paths | Paxos overhead (4 round trips) | Accept eventual consistency |

## When to Use Cassandra

| Good Fit | Poor Fit |
|----------|----------|
| Time-series data (IoT, metrics) | Complex transactions |
| High-velocity writes | Strong consistency required |
| Multi-datacenter deployment | Relational queries (joins) |
| Linear scalability needed | Small dataset (< 1 TB) |
| Always-on, no downtime | Aggregation-heavy workloads |

**Links**: [[MongoDB]] | [[SQL vs NoSQL Databases]] | [[Time Series Databases]] | [[Database Sharding]] | [[Database Engines Compared]]
