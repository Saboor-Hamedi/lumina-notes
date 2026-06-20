---
id: a1b2c3d4-1144-4000-8000-000000000144
title: SQL Query Optimization
language: markdown
tags:
  - system-design
  - databases
  - sql
  - query-optimization
selection: null
isPinned: false
customIcon: null
timestamp: 1781964251402
---

**Links**: [[SQL JOIN Operations]] | [[Database Indexing Deep Dive]] | [[Database Indexing Strategies]] | [[PostgreSQL Performance Tuning]] | [[Database Transaction Isolation Levels]] | [[LSM-Tree Storage Engines]]


# SQL Query Optimization

Optimizing SQL queries involves understanding execution plans, indexes, statistics, and query structure.

## Execution Plans

The query planner produces a tree of nodes representing how it will access and join data. Reading an execution plan means tracing from the innermost node outward — each node feeds rows to its parent. The most common node types, ordered from cheapest to most expensive, tell you where the database is spending its time.

**Plan nodes** (from cheapest to most expensive):

```
Index Only Scan  (fastest — needs covering index)
Index Scan       (fast — needs good selectivity)
Bitmap Scan      (moderate — multiple index matches)
Seq Scan         (slow — full table read)
```

Always start with `EXPLAIN ANALYZE` rather than bare `EXPLAIN` — the actual execution time and row counts reveal when the planner's estimates are wrong.

```sql
-- PostgreSQL
EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON) SELECT * FROM orders WHERE user_id = 123;

-- MySQL
EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 123;
```

## Key Metrics in EXPLAIN ANALYZE

The output of `EXPLAIN ANALYZE` contains several signal values that point to specific problems. A mismatch between estimated and actual rows means stale statistics. High `rows removed by filter` means the index is missing a condition. Low cache hit ratios point to memory pressure.

| Metric | What to Look For | Action |
|--------|------------------|--------|
| `actual rows` vs `estimated rows` | Large mismatch → stale stats | `ANALYZE table;` |
| `actual time` | High at one step | Optimize that step |
| `rows removed by filter` | Many → missing index | Add index |
| `Shared Hit Blocks` | Few → cache misses | Increase `shared_buffers` |
| `I/O Time` | High → disk reads | Add indexes, increase cache |

## Common Optimization Patterns

Most slow queries fall into a handful of recurring patterns. Recognising these saves time debugging — each pattern has a predictable fix.

### 1. Missing Index

The most common issue. The planner falls back to a sequential scan when no index supports the WHERE clause.

```sql
-- Slow (Seq Scan on large table)
SELECT * FROM orders WHERE user_id = 123;

-- Fix: Add index
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

### 2. Wrong Join Order

Modern optimisers usually pick the correct join order, but complex queries with many tables can confuse them. If you see a large table joined before a small one in the plan, force the order or simplify the query.

```sql
-- Join small table first
SELECT *
FROM small_table s
JOIN large_table l ON l.small_id = s.id;

-- If planner gets it wrong: SET join_collapse_limit = 1;
```

### 3. Non-SARGable WHERE Clauses

A WHERE clause is SARGable (Search ARGument ABLE) when the database can use an index directly. Wrapping a column in a function breaks index access — the planner must evaluate the function for every row before it can check the condition.

```sql
-- SARGable: can use index
WHERE created_at >= '2026-01-01' AND created_at < '2026-02-01'  -- ✔

-- Non-SARGable (can't use index efficiently)
WHERE DATE(created_at) = '2026-01-15'                            -- ✗
WHERE YEAR(created_at) = 2026                                    -- ✗
WHERE LOWER(email) = 'alice@example.com'                         -- ✗
WHERE CAST(user_id AS text) = '123'                              -- ✗
WHERE id + 1 = 10                                                -- ✗
WHERE name LIKE '%smith%'                                        -- ✗

-- Fixes
CREATE INDEX idx_created_date ON orders((created_at::date));
WHERE name ILIKE '%smith%'  -- (use pg_trgm index)
```

### 4. SELECT * (Reads unnecessary columns)

Fetching all columns forces the database to read from the heap (the main table storage) even when all the filtering columns are in an index. Selecting only the columns you need enables an index-only scan, skipping the heap read entirely.

```sql
-- Bad: reads all columns from heap
SELECT * FROM users WHERE email = 'alice@example.com';

-- Good: index-only scan possible
SELECT email, name FROM users WHERE email = 'alice@example.com';
```

### 5. N+1 Queries

An N+1 query runs one query to fetch a list and then one query per item in that list. This is common in ORMs loading relationships lazily. The fix is to batch the per-item queries into a single JOIN or subquery.

```sql
-- N+1: For each user, query their orders
-- Solution: Batch fetch with LATERAL JOIN
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

Beyond basic indexing and query rewriting, several advanced techniques handle specific performance patterns. These include replacing self-joins with window functions, using partial indexes for hot queries, and pre-computing expensive aggregates with materialized views.

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

When most queries filter on the same constant (e.g., `status = 'active'`), a partial index indexed only on that subset is much smaller and faster than a full-table index. The database only maintains index entries matching the WHERE condition.

```sql
-- Query runs 95% of the time with status = 'active'
CREATE INDEX idx_orders_active ON orders(created_at DESC) WHERE status = 'active';

SELECT * FROM orders WHERE status = 'active' ORDER BY created_at DESC LIMIT 10;
-- → Tiny index, blazing fast
```

### Materialized Views for Aggregates

For dashboards and recurring reports, materialized views store pre-computed aggregates on disk. The tradeoff is data freshness — you must refresh the view periodically, but reads become trivial index lookups.

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

Fixing slow queries follows a repeatable loop. Start by finding the worst offenders, diagnose with EXPLAIN ANALYZE, apply the fix, verify the improvement, then monitor to make sure it stays fast.

```
1. Find slow query (pg_stat_statements, slow query log)
2. EXPLAIN ANALYZE
3. Identify expensive node
4. Add index / rewrite query
5. Re-test with EXPLAIN ANALYZE
6. Monitor (pg_stat_user_indexes for usage)
```

## Table Statistics

The query planner relies on statistics about table contents to estimate row counts and choose the best plan. Stale or insufficient statistics cause the planner to pick bad strategies. Regularly running `ANALYZE` and increasing the statistics target for key columns improves plan quality.

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

Several tools help identify and diagnose slow queries. `pg_stat_statements` surfaces the top time-consuming queries globally, `auto_explain` logs plans for queries exceeding a threshold, and visualisers like `depesz.com` make complex plans readable.

| Tool | Purpose |
|------|---------|
| pg_stat_statements | Find top queries by total time |
| auto_explain | Log slow queries with plans |
| pgBadger | Parse PostgreSQL logs |
| pganalyze | Commercial performance monitoring |
| EXPLAIN.depesz.com | Visualize execution plans |
| pgMustard | Commercial plan analysis |

**Links**: [[Database Indexing Strategies]] | [[Window Functions and CTEs]] | [[PostgreSQL]] | [[Performance Testing]] | [[B-Tree Dynamics]]
