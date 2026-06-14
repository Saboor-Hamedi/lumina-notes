---
id: a1b2c3d4-1143-4000-8000-000000000143
title: Database Indexing Strategies
language: markdown
tags: [system-design, databases, indexing]
selection: null
isPinned: false
timestamp: 1781500001143
---

**Links**: [[Database Indexing Deep Dive]] | [[SQL Query Optimization]] | [[LSM-Tree Storage Engines]] | [[Database Engines Compared]] | [[PostgreSQL Performance Tuning]] | [[Database Transaction Isolation Levels]]


# Database Indexing Strategies

Indexes accelerate data retrieval at the cost of slower writes and storage. Choosing the right indexes is critical for query performance.

## Index Types

| Type | Structure | Use Case |
|------|-----------|----------|
| B-Tree | Balanced tree | General purpose, range queries |
| Hash | Hash table | Equality lookups |
| GiST | Generalized Search Tree | Full-text, spatial |
| GIN | Generalized Inverted Index | Arrays, JSONB |
| BRIN | Block Range Index | Large, sorted data |
| Bitmap | Bitmap | Low-cardinality columns |

## B-Tree Index (Most Common)

```sql
-- Single column
CREATE INDEX idx_users_email ON users(email);

-- Composite (multi-column)
CREATE INDEX idx_users_name_age ON users(last_name, first_name, age);

-- Order matters: column order determines sort order
-- Useful for: WHERE, ORDER BY, JOIN, GROUP BY
```

**Rules for composite indexes**:
- Leftmost prefix: `(a, b, c)` covers `WHERE a=`, `WHERE a= AND b=`, `WHERE a= AND b= AND c=`
- Does NOT cover `WHERE b=` or `WHERE c=`
- Place equality conditions first, range conditions last

```sql
-- Good: WHERE status = 'active' AND created_at > '2026-01-01'
CREATE INDEX idx_status_created ON orders(status, created_at);
-- Equality column first, range column second

-- Skip scan (PostgreSQL): WHERE b = 5 (without a)
-- Uses index (a, b) via skip scan in PG 16+
```

## Index Scan Types

| Scan Type | What Happens | When |
|-----------|-------------|------|
| Sequential Scan | Reads entire table | No index, or table is small |
| Index Scan | Navigates B-Tree → reads heap page | Returns few rows |
| Index-Only Scan | All data in index, no heap access | Index covers all queried columns |
| Bitmap Scan | Builds bitmap of matching pages → reads heap | Returns moderate row count |

```sql
-- Check scan type
EXPLAIN (ANALYZE, BUFFERS) SELECT * FROM users WHERE email = 'alice@example.com';

-- Index Only Scan (all columns in index)
CREATE INDEX idx_users_email_covering ON users(email) INCLUDE (name, age);
-- Now: SELECT email, name, age FROM users WHERE email = '...' → Index Only Scan
```

## Partial Index

```sql
-- Index only a subset of rows
CREATE INDEX idx_active_orders ON orders(created_at) WHERE status = 'active';

-- For queries: SELECT * FROM orders WHERE status = 'active' AND created_at > '2026-01-01'
-- Smaller index, faster maintenance
```

## Covering Index

```sql
-- Include additional columns to avoid heap lookups
CREATE INDEX idx_orders_user_covering
    ON orders(user_id)
    INCLUDE (total, status, created_at);

-- Query: SELECT total, status FROM orders WHERE user_id = 123
-- → Index Only Scan (no heap access needed)
```

## Expression Index

```sql
-- Index the result of an expression
CREATE INDEX idx_users_lower_email ON users(LOWER(email));

-- Query: SELECT * FROM users WHERE LOWER(email) = 'alice@example.com'
-- Without this index: sequential scan (casts every row)
-- With this index: index scan

-- JSONB expression index
CREATE INDEX idx_orders_currency ON orders((details->>'currency'));
```

## Index Maintenance

```sql
-- Check index size
SELECT
    indexrelid::regclass AS index_name,
    pg_size_pretty(pg_relation_size(indexrelid)) AS size
FROM pg_stat_user_indexes
WHERE schemaname = 'public';

-- Rebuild index (reduces bloat)
REINDEX INDEX idx_users_email;
-- Or concurrently (no lock):
REINDEX INDEX CONCURRENTLY idx_users_email;

-- Bloat estimation
SELECT schemaname, tablename, n_dead_tup, n_live_tup,
       round(n_dead_tup * 100.0 / (n_live_tup + 1), 2) AS dead_pct
FROM pg_stat_user_tables
WHERE n_live_tup > 0
ORDER BY dead_pct DESC;
```

## Indexing Anti-Patterns

```sql
-- BAD: Over-indexing (too many indexes on one table)
CREATE INDEX idx_col1 ON t(col1);
CREATE INDEX idx_col2 ON t(col2);
CREATE INDEX idx_col3 ON t(col3);
CREATE INDEX idx_col4 ON t(col4);
-- Each index slows INSERT/UPDATE/DELETE

-- BETTER: Covering index for common query patterns
CREATE INDEX idx_common ON t(col1, col2) INCLUDE (col3, col4);

-- BAD: Index on low-cardinality column
CREATE INDEX idx_status ON orders(status);  -- Only 3 values: pending, active, done
-- Bitmap scan or sequential scan is faster than index scan

-- BAD: Index on frequently updated column
CREATE INDEX idx_counter ON stats(counter);  -- UPDATE counter = counter + 1
-- Index maintenance overhead on every update

-- BAD: Functional index when not needed
SELECT * FROM users WHERE email = 'alice@example.com';  -- Regular B-tree is fine
```

## Index Selection Guide

| Query Pattern | Index Type | Example |
|---------------|------------|---------|
| `WHERE col = val` | B-Tree or Hash | `email = 'alice@example.com'` |
| `WHERE col > val` | B-Tree | `created_at > '2026-01-01'` |
| `WHERE col IN (...)` | B-Tree | `status IN ('active', 'pending')` |
| `WHERE col LIKE 'prefix%'` | B-Tree (text pattern ops) | `name LIKE 'Ali%'` |
| `WHERE col ILIKE '%substring%'` | Trigram (pg_trgm) | `name ILIKE '%smith%'` |
| `WHERE col @@ to_tsquery(...)` | GIN | Full-text search |
| `WHERE col @> '{"key": "val"}'` | GIN | JSONB containment |
| `ORDER BY col` | B-Tree with sort direction | `ORDER BY created_at DESC` |
| `JOIN ... ON a.col = b.col` | B-Tree on join column | `ON orders.user_id = users.id` |

## Monitoring Unused Indexes

```sql
-- Find unused indexes
SELECT
    schemaname,
    tablename,
    indexname,
    idx_scan,
    idx_tup_read,
    idx_tup_fetch
FROM pg_stat_user_indexes
WHERE idx_scan = 0
ORDER BY tablename;
```

**Links**: [[SQL Query Optimization]] | [[B-Tree Dynamics]] | [[LSM-Tree Storage Engines]] | [[PostgreSQL]] | [[Performance Testing]]
