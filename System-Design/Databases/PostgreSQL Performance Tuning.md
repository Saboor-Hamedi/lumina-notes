---
id: a1b2c3d4-1180-4000-8000-000000000180
title: PostgreSQL Performance Tuning
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001180
---
# PostgreSQL Performance Tuning

PostgreSQL performance tuning involves configuring memory, I/O, query planning, and connection management.

## Memory Configuration

```ini
# postgresql.conf — Memory settings

# shared_buffers: 25% of RAM (typically)
shared_buffers = 4GB

# effective_cache_size: 50-75% of RAM
# Tells query planner how much cache is available
effective_cache_size = 12GB

# work_mem: per-operation sort/hash memory
# Too high = out of memory, too low = disk spills
work_mem = 64MB

# maintenance_work_mem: VACUUM, CREATE INDEX, etc.
maintenance_work_mem = 1GB

# wal_buffers: WAL write buffer (16-64MB typical)
wal_buffers = 16MB

# huge_pages: try to use huge pages (reduces TLB misses)
huge_pages = try
```

## Connection Management

```ini
# max_connections: limit (each connection needs ~10MB)
max_connections = 200

# Use connection pooler (PgBouncer) for high connections
# PgBouncer: 2000 app connections → 50 database connections

# Connection timeout
idle_in_transaction_session_timeout = '5min'
statement_timeout = '30s'
lock_timeout = '5s'
```

## Checkpoint and WAL

```ini
# Checkpoint frequency
# Frequent checkpoints = smoother I/O but more overhead
checkpoint_timeout = '15min'
max_wal_size = '4GB'
min_wal_size = '1GB'

# WAL settings
wal_level = replica
wal_buffers = '64MB'
wal_writer_delay = '200ms'
wal_sync_method = fdatasync  # Linux default
# For faster but less safe: wal_sync_method = open_datasync

# Full page writes (protect against partial page writes)
full_page_writes = on   # Required for crash safety
```

## Autovacuum Tuning

```ini
# Autovacuum — prevents bloat and transaction ID wraparound
autovacuum = on
autovacuum_max_workers = 4
autovacuum_naptime = '1min'
autovacuum_vacuum_threshold = 50
autovacuum_vacuum_scale_factor = 0.01       # 1% of table
autovacuum_vacuum_cost_limit = 200          # Throttle I/O
autovacuum_vacuum_cost_delay = '20ms'

# Heavy-write tables: per-table tuning
ALTER TABLE orders SET (autovacuum_vacuum_scale_factor = 0.005);
ALTER TABLE orders SET (autovacuum_vacuum_threshold = 10000);
```

## Query Planning

```ini
# Planner cost constants
seq_page_cost = 1.0
random_page_cost = 1.1      # SSD: 1.1, HDD: 4.0
cpu_tuple_cost = 0.01
cpu_index_tuple_cost = 0.005
cpu_operator_cost = 0.0025
parallel_tuple_cost = 0.1
parallel_setup_cost = 1000.0

# Enable parallel queries
max_parallel_workers_per_gather = 4
max_parallel_workers = 8
parallel_leader_participation = on

# Genetic query optimizer for complex joins
geqo = on
geqo_threshold = 12           # Use GEQO for 12+ tables
```

## Monitoring Queries

```sql
-- pg_stat_statements: Top queries by total time
SELECT
    query,
    calls,
    total_exec_time / 1000 AS total_seconds,
    mean_exec_time,
    rows,
    shared_blks_hit,
    shared_blks_read
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 10;

-- Currently running queries
SELECT
    pid,
    now() - pg_stat_activity.query_start AS duration,
    query,
    state,
    wait_event_type,
    wait_event
FROM pg_stat_activity
WHERE state != 'idle'
ORDER BY duration DESC;

-- Index usage
SELECT
    schemaname,
    tablename,
    indexname,
    idx_scan,
    idx_tup_read,
    idx_tup_fetch
FROM pg_stat_user_indexes
ORDER BY idx_scan ASC
LIMIT 20;  -- Least used indexes (candidates for removal)
```

## Vacuum and Bloat

```sql
-- Check table bloat
SELECT
    schemaname,
    tablename,
    n_dead_tup,
    n_live_tup,
    round(n_dead_tup * 100.0 / (n_live_tup + 1), 2) AS dead_pct
FROM pg_stat_user_tables
WHERE n_live_tup > 0
ORDER BY dead_pct DESC
LIMIT 10;

-- Aggressive vacuum
VACUUM (VERBOSE, ANALYZE) orders;

-- Rebuild table to eliminate bloat
CLUSTER VERBOSE orders USING idx_orders_created_at;
-- Or: VACUUM FULL orders;  (takes ACCESS EXCLUSIVE lock)
```

## Partitioning

```sql
-- Range partitioning (PostgreSQL 10+)
CREATE TABLE orders (
    id BIGSERIAL,
    order_date DATE NOT NULL,
    customer_id BIGINT,
    amount DECIMAL(10,2)
) PARTITION BY RANGE (order_date);

CREATE TABLE orders_2026_q1 PARTITION OF orders
    FOR VALUES FROM ('2026-01-01') TO ('2026-04-01');

CREATE TABLE orders_2026_q2 PARTITION OF orders
    FOR VALUES FROM ('2026-04-01') TO ('2026-07-01');

-- Partition pruning: query only scans relevant partitions
EXPLAIN SELECT * FROM orders WHERE order_date = '2026-05-15';
-- → Only scans orders_2026_q2
```

## Key Metrics to Monitor

```sql
-- Cache hit ratio (>99% is good)
SELECT
    sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) * 100 AS cache_hit_ratio
FROM pg_statio_user_tables;

-- Index hit ratio (>95% is good)
SELECT
    sum(idx_blks_hit) / (sum(idx_blks_hit) + sum(idx_blks_read)) * 100 AS idx_hit_ratio
FROM pg_statio_user_indexes;

-- Transaction wraparound (keep below 2 billion)
SELECT
    datname,
    age(datfrozenxid) AS xid_age,
    round(100 * age(datfrozenxid) / 2000000000, 2) AS xid_pct_used
FROM pg_database;
```

## pgtune Recommendations

```bash
# Use pgtune to generate optimized config
pgtune -i postgresql.conf -o postgresql-tuned.conf --type=web
# Types: web, oltp, dw, desktop, mixed
```
