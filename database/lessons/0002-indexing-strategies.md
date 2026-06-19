# Lesson 2: Indexing Strategies

**Lesson 2** · ~25 min · Hands-on

> [!NOTE] Mission tie-in
> Indexes are the #1 lever for query performance. A missing index turns a 10ms query into a 10s one. A wrong index wastes RAM and slows writes. Choosing the right index for the right query pattern is what separates intermediate from advanced PostgreSQL.

## What You'll Learn

- How B-tree indexes work internally
- When to use GiST, GIN, and BRIN indexes
- How to verify an index is being used
- Multi-column and partial indexes

## Recap: The Index Scan

From Lesson 1, you saw an `Index Scan` replace a `Seq Scan` after adding an index. Here's what actually happens:

```
Without index:  Seq Scan → reads EVERY row → checks WHERE → keeps matches
With index:     Index Scan → walks B-tree → finds matching heap addresses → fetches only those rows
```

A B-tree index is a balanced tree. To find `WHERE name = 'User 5000'` among 10K rows, PostgreSQL walks ~log₂(10000) ≈ 14 nodes instead of checking all 10K rows.

## Index Types

| Index Type | What It's Good At | Example Query |
|-----------|-------------------|---------------|
| **B-tree** (default) | Equality and range queries, sorting | `WHERE id = 5`, `WHERE date > '2026-01-01'`, `ORDER BY name` |
| **Hash** | Simple equality lookups | `WHERE session_id = 'abc'` |
| **GiST** | Full-text search, geometry, ranges | `WHERE doc @@ to_tsquery('cat & dog')` |
| **GIN** | JSONB, arrays, full-text tsvector | `WHERE data @> '{"key": "val"}'` |
| **BRIN** | Large, physically-sorted tables | `WHERE created_at BETWEEN '2026-01-01' AND '2026-03-01'` on a log table |

## B-tree in Detail

```sql
-- Single column
CREATE INDEX idx_users_email ON users (email);

-- Multi-column (order matters!)
CREATE INDEX idx_users_name_created ON users (name, created_at);

-- Unique
CREATE UNIQUE INDEX idx_users_email_unique ON users (email);
```

For a multi-column index on `(name, created_at)`, the following queries can use it:

```sql
-- Can use index
SELECT * FROM users WHERE name = 'Alice';
SELECT * FROM users WHERE name = 'Alice' AND created_at > '2026-01-01';
SELECT * FROM users ORDER BY name, created_at;

-- Cannot use index (skips the leading column)
SELECT * FROM users WHERE created_at > '2026-01-01';
```

> [!TIP] Put the most selective column first in a multi-column index. Columns used with `=` should come before columns used with range (`>`, `<`, `BETWEEN`).

## GiST and GIN — Full-Text Search

```sql
-- GiST for full-text search
CREATE INDEX idx_docs_fts ON documents USING GIST (to_tsvector('english', body));
SELECT * FROM documents WHERE to_tsvector('english', body) @@ to_tsquery('postgresql & index');

-- GIN for JSONB
CREATE INDEX idx_events_data ON events USING GIN (data jsonb_path_ops);
SELECT * FROM events WHERE data @> '{"action": "login"}';
```

> [!TIP] GIN indexes are larger than GiST but faster to query and slower to build. For JSONB, GIN with `jsonb_path_ops` is the standard choice.

## BRIN — Block Range Index

BRIN is for enormous tables where data is stored in physical order:

```sql
CREATE INDEX idx_logs_created ON logs USING BRIN (created_at)
  WITH (pages_per_range = 32);
```

If your `logs` table has 100M rows inserted chronologically, a B-tree on `created_at` would be huge (multiple GB). A BRIN index scans metadata about which page ranges contain which values — much smaller, but only useful for range queries on naturally-ordered data.

## Partial Indexes

Index only a subset of rows — smaller, faster:

```sql
CREATE INDEX idx_orders_shipped ON orders (created_at) WHERE status = 'shipped';
CREATE INDEX idx_users_active ON users (email) WHERE last_login > now() - interval '30 days';
```

These are powerful when you frequently query a subset: "find recent shipped orders" or "find active users."

## Covering Indexes (INCLUDE)

Store extra columns in the index to allow Index Only Scans:

```sql
CREATE INDEX idx_users_email_include_name ON users (email) INCLUDE (name);

-- This query can use Index Only Scan (no heap visit needed):
SELECT email, name FROM users WHERE email = 'alice@example.com';
```

## Hands-On

```sql
-- Use the users/orders tables from Lesson 1

-- 1. Check what queries are slow (requires pg_stat_statements)
SELECT query, calls, total_exec_time, mean_exec_time, rows
FROM pg_stat_statements
ORDER BY mean_exec_time DESC
LIMIT 5;

-- 2. See which indexes exist
SELECT schemaname, tablename, indexname, indexdef
FROM pg_indexes
WHERE tablename NOT LIKE 'pg_%';

-- 3. Check index usage
SELECT schemaname, tablename, indexname, idx_scan, idx_tup_read
FROM pg_stat_user_indexes
ORDER BY idx_scan ASC
LIMIT 10;

-- 4. Create and test a partial index
EXPLAIN ANALYZE SELECT * FROM orders WHERE status = 'shipped' AND created_at > now() - interval '7 days';
CREATE INDEX idx_orders_shipped_recent ON orders (created_at) WHERE status = 'shipped';
EXPLAIN ANALYZE SELECT * FROM orders WHERE status = 'shipped' AND created_at > now() - interval '7 days';
```

## Self-Quiz

<details>
<summary><strong>1.</strong> For an index on <code>(city, last_name)</code>, which queries can use it?</summary>

`WHERE city = 'NYC'` (yes), `WHERE city = 'NYC' AND last_name = 'Smith'` (yes), `WHERE last_name = 'Smith'` (no — skips the leading column). The leading column must be used for the index to be effective.
</details>

<details>
<summary><strong>2.</strong> When would you pick BRIN over B-tree?</summary>

On huge, append-only tables (like logs) where data is physically ordered by the indexed column. BRIN is tiny compared to B-tree but only works for range queries.
</details>

<details>
<summary><strong>3.</strong> What's the advantage of <code>CREATE INDEX ... WHERE status = 'shipped'</code>?</summary>

It's a partial index — only indexes rows matching the WHERE clause. Smaller, faster, and less write overhead. Use it when you always query the same subset.
</details>

## Next Steps

- Run the hands-on on your own data — check for unused indexes with the query above
- Move to [Lesson 3: MVCC Internals](0003-mvcc-internals.md)

> [!NOTE] Ask followup questions!
> Not sure which index type to use for your data? Paste your query and schema — we'll figure it out.

---

*References: [Explain Output Reference](../reference/0001-explain-output-reference.md) | Vault: [[System-Design/Databases/Database Indexing Deep Dive]]*
