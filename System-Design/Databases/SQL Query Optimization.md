---
id: a1b2c3d4-1144-4000-8000-000000000144
title: SQL Query Optimization
language: markdown
tags: [system-design, databases, sql, query-optimization]
selection: null
isPinned: false
timestamp: 1781500001144
---

**Links**: [[SQL JOIN Operations]] | [[Database Indexing Deep Dive]] | [[Database Indexing Strategies]] | [[PostgreSQL Performance Tuning]] | [[Database Transaction Isolation Levels]] | [[LSM-Tree Storage Engines]]


# SQL Query Optimization

Optimizing SQL queries involves understanding execution plans, indexes, statistics, and query structure.

## Execution Plans

```sql
-- PostgreSQL
EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON) SELECT * FROM orders WHERE user_id = 123;

-- MySQL
EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 123;

-- Read from inside out, top-down
-- Look for: sequential scans on large tables, nested loops on many rows
```

**Plan nodes** (from cheapest to most expensive):

```
Index Only Scan  (fastest — needs covering index)
Index Scan       (fast — needs good selectivity)
Bitmap Scan      (moderate — multiple index matches)
Seq Scan         (slow — full table read)
```

## Key Metrics in EXPLAIN ANALYZE

| Metric | What to Look For | Action |
|--------|------------------|--------|
| `actual rows` vs `estimated rows` | Large mismatch → stale stats | `ANALYZE table;` |
| `actual time` | High at one step | Optimize that step |
| `rows removed by filter` | Many → missing index | Add index |
| `Shared Hit Blocks` | Few → cache misses | Increase `shared_buffers` |
| `I/O Time` | High → disk reads | Add indexes, increase cache |

## Common Optimization Patterns

### 1. Missing Index

```sql
-- Slow (Seq Scan on large table)
SELECT * FROM orders WHERE user_id = 123;

-- Fix: Add index
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

### 2. Wrong Join Order

```sql
-- Join small table first
SELECT *
FROM small_table s
JOIN large_table l ON l.small_id = s.id;  -- Database usually handles this

-- But if it doesn't: SET join_collapse_limit = 1; (forces manual order)
```

### 3. Non-SARGable WHERE Clauses

```sql
-- SARGable: Search ARGument ABLE (can use index)
WHERE created_at >= '2026-01-01' AND created_at < '2026-02-01'  -- ✔

-- Non-SARGable (can't use index efficiently)
WHERE DATE(created_at) = '2026-01-15'                            -- ✗
WHERE YEAR(created_at) = 2026                                    -- ✗
WHERE LOWER(email) = 'alice@example.com'                         -- ✗
WHERE CAST(user_id AS text) = '123'                              -- ✗
WHERE id + 1 = 10                                                -- ✗
WHERE name LIKE '%smith%'                                        -- ✗

-- Fixes (use expression index or rewrite):
CREATE INDEX idx_created_date ON orders((created_at::date));
WHERE name ILIKE '%smith%'  -- (use pg_trgm index)
```

### 4. SELECT * (Reads unnecessary columns)

```sql
-- Bad: reads all columns from heap
SELECT * FROM users WHERE email = 'alice@example.com';

-- Good: index-only scan possible
SELECT email, name FROM users WHERE email = 'alice@example.com';
```

### 5. N+1 Queries

```sql
-- N+1: For each user, query their orders
-- Solution: Batch fetch
SELECT u.*, o.*
FROM users u
LEFT JOIN LATERAL (
    SELECT * FROM orders
    WHERE user_id = u.id
    ORDER BY created_at DESC
    LIMIT 1
) o ON true;
```

## Advanced Optimization

### Window Functions vs Self-Joins

```sql
-- Slow (self-join)
SELECT u1.*, u2.name AS manager_name
FROM users u1
LEFT JOIN users u2 ON u1.manager_id = u2.id;

-- Same result, better plan
SELECT *, (SELECT name FROM users WHERE id = u.manager_id) AS manager_name
FROM users u;
```

### Partial Indexes for Common Queries

```sql
-- Query runs 95% of the time with status = 'active'
CREATE INDEX idx_orders_active ON orders(created_at DESC) WHERE status = 'active';

SELECT * FROM orders WHERE status = 'active' ORDER BY created_at DESC LIMIT 10;
-- → Tiny index, blazing fast
```

### Materialized Views for Aggregates

```sql
CREATE MATERIALIZED VIEW daily_sales AS
SELECT
    DATE(created_at) AS day,
    COUNT(*) AS orders,
    SUM(total) AS revenue
FROM orders
GROUP BY DATE(created_at);

-- Refresh periodically
REFRESH MATERIALIZED VIEW daily_sales;

-- Query: SELECT * FROM daily_sales WHERE day > '2026-01-01';
```

## Optimization Workflow

```
1. Find slow query (pg_stat_statements, slow query log)
2. EXPLAIN ANALYZE
3. Identify expensive node
4. Add index / rewrite query
5. Re-test with EXPLAIN ANALYZE
6. Monitor (pg_stat_user_indexes for usage)
```

## Table Statistics

```sql
-- Update statistics for query planner
ANALYZE orders;

-- Check statistics
SELECT
    attname,
    n_distinct,
    most_common_vals,
    most_common_freqs,
    correlation
FROM pg_stats
WHERE tablename = 'orders';

-- Increase statistics target for better estimates
ALTER TABLE orders ALTER COLUMN user_id SET STATISTICS 1000;
ANALYZE orders;
```

## Tools

| Tool | Purpose |
|------|---------|
| pg_stat_statements | Find top queries by total time |
| auto_explain | Log slow queries with plans |
| pgBadger | Parse PostgreSQL logs |
| pganalyze | Commercial performance monitoring |
| EXPLAIN.depesz.com | Visualize execution plans |
| pgMustard | Commercial plan analysis |

**Links**: [[Database Indexing Strategies]] | [[Window Functions and CTEs]] | [[PostgreSQL]] | [[Performance Testing]] | [[B-Tree Dynamics]]
