---
id: 0001-reading-query-plans
title: 0001-reading-query-plans
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781790799874.635
---
# Reading PostgreSQL Query Plans

**Lesson 1** · ~20 min · Hands-on

> [!NOTE] Mission tie-in
> Reading query plans is the #1 skill that separates "I use PostgreSQL" from "I master PostgreSQL." Every performance tuning session, every slow-query debugging story, every schema design decision traces back to understanding what the planner tells you.

## What You'll Learn

- How a SQL query becomes an execution plan
- How to read `EXPLAIN ANALYZE` output — costs, rows, node types
- How to spot the bottleneck in any plan

## Prerequisites

You need a running PostgreSQL instance. Pick one:

**A — Local install**
```bash
psql -U postgres
```

**B — Docker**
```bash
docker run --name pg-lesson -e POSTGRES_PASSWORD=pgpass -d -p 5432:5432 postgres:16
docker exec -it pg-lesson psql -U postgres
```

## The Plan Anatomy

Every query plan is a tree of **nodes**. Each node has a type (how it accesses or joins data) and a cost estimate. Read from the **inside out**:

```
QUERY PLAN
────────────────────────────────────────────────────
 Sort  (cost=100.50..102.75 rows=900 width=40)      ← outermost
   Sort Key: u.email
   └─ Hash Join  (cost=20.25..60.50 rows=900 width=40)  ← join
        Hash Cond: (o.user_id = u.id)
        ├─ Seq Scan on orders o  (cost=0.00..35.00 rows=1000 width=20)  ← leaf
        └─ Hash  (cost=18.00..18.00 rows=900 width=20)  ← inner
             └─ Seq Scan on users u  (cost=0.00..18.00 rows=900 width=20)  ← leaf
```

| Field | Meaning |
|-------|---------|
| `cost=100.50..102.75` | Startup cost (to emit first row) .. total cost (to finish). Measured in arbitrary units — what matters is **relative cost between nodes**, not the absolute number. |
| `rows=900` | Estimated number of rows this node will output. The planner's guess. |
| `width=40` | Estimated average width of each output row in bytes. |

### Node Types Cheat Sheet

| Scan Type | When It's Used |
|-----------|----------------|
| `Seq Scan` | Full table scan. Fine for small tables, bad for large ones. The planner picks this when it thinks it'll read >5-10% of the table. |
| `Index Scan` | Walks the B-tree, then fetches heap tuples. Good for selective queries. |
| `Index Only Scan` | All needed columns are in the index. No heap fetch. Fastest possible scan. |
| `Bitmap Index Scan` + `Bitmap Heap Scan` | Scans index to build a bitmap of pages, then fetches heap pages. Used when the planner estimates many rows but not enough for a full Seq Scan. |

| Join Type | When It's Used |
|-----------|----------------|
| `Nested Loop` | For each row in outer, scan inner. Best when outer is small and inner has an index. |
| `Hash Join` | Build hash table from inner, probe with outer. Best when joining medium, unsorted tables. |
| `Merge Join` | Both inputs sorted. Walk both in parallel. Best for large, pre-sorted datasets. |

## Hands-On: Read Your First Plans

Run these commands in `psql`. Build **muscle memory** for reading plan output.

### 1. Set up test data

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email TEXT NOT NULL,
  name TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  user_id INT NOT NULL REFERENCES users(id),
  amount DECIMAL(10,2) NOT NULL,
  status TEXT NOT NULL DEFAULT 'pending',
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Insert sample data
INSERT INTO users (email, name)
  SELECT 'user' || n || '@example.com', 'User ' || n
  FROM generate_series(1, 10000) n;

INSERT INTO orders (user_id, amount, status, created_at)
  SELECT
    (random() * 9999 + 1)::INT,
    (random() * 500 + 10)::DECIMAL(10,2),
    CASE WHEN random() < 0.3 THEN 'shipped'
         WHEN random() < 0.6 THEN 'pending'
         ELSE 'cancelled' END,
    now() - (random() * 365 || ' days')::INTERVAL
  FROM generate_series(1, 50000) n;
```

### 2. Full table scan

```sql
EXPLAIN ANALYZE SELECT * FROM users WHERE name = 'User 5000';
```

You should see a `Seq Scan`. Note the `actual rows` vs `estimated rows` — how far apart are they? Now add an index and see the difference:

```sql
CREATE INDEX idx_users_name ON users (name);

EXPLAIN ANALYZE SELECT * FROM users WHERE name = 'User 5000';
```

The plan should now show an `Index Scan` — much lower cost, faster execution.

### 3. Join and aggregate

```sql
EXPLAIN ANALYZE
SELECT u.name, COUNT(o.id) AS order_count
FROM users u
JOIN orders o ON o.user_id = u.id
WHERE o.status = 'shipped'
GROUP BY u.id, u.name
ORDER BY order_count DESC
LIMIT 10;
```

This produces a multi-node plan. Read it inside-out:

1. Start at the deepest indented node (the scan of `orders` or `users`)
2. Work upward: join → group → sort → limit
3. At each node, notice the `actual rows` vs `estimated rows` — a large mismatch means the planner has bad statistics

## Spotting Trouble

> [!WARNING] Red Flag 1: Row estimate mismatch
> If `actual rows` differs from `estimated rows` by 10x or more, the planner is working with stale statistics. Run `ANALYZE;` to refresh them.

> [!WARNING] Red Flag 2: Seq Scan on a large table
> If you see `Seq Scan` on a table with millions of rows, you likely need an index — unless you're truly reading most of the table.

> [!WARNING] Red Flag 3: Nested Loop with a large outer
> A Nested Loop join where the outer side produces thousands of rows means the inner side is scanned thousands of times. This kills performance.

> [!TIP] Pro tip
> Always use `EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)` for deep analysis. The JSON format is machine-readable and includes per-node buffer hit/miss stats:
> ```sql
> EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)
> SELECT * FROM users WHERE name = 'User 5000';
> ```

## Run This Diagnostic

Copy-paste this into psql to find the worst-performing queries in your actual databases:

```sql
SELECT
  query,
  calls,
  mean_exec_time,
  shared_blks_hit,
  shared_blks_read
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 10;
```

> [!NOTE] Requires `CREATE EXTENSION pg_stat_statements;` — see [Explain Output Reference](../reference/0001-explain-output-reference.md) for the full query.

## Self-Quiz

<details>
<summary><strong>1.</strong> You see <code>actual rows=1000</code> but <code>estimated rows=50</code>. What's likely wrong?</summary>

Stale table statistics. Run `ANALYZE` (or `VACUUM ANALYZE`) to refresh them. The planner picks bad strategies when its row estimates are off.
</details>

<details>
<summary><strong>2.</strong> Your query joins a small table (100 rows) with a large indexed table (1M rows). Which join type should the planner pick?</summary>

**Nested Loop** — for each of the 100 rows, probe the large table's index. This is O(100 * log(1M)) ~ 100 * ~20 = 2000 operations. A Hash Join would hash 1M rows — wasteful.
</details>

<details>
<summary><strong>3.</strong> You see <code>Sort Method: external merge Disk: 3200kB</code>. What's happening?</summary>

The sort spilled to disk because `work_mem` was too small. Increase `work_mem` (e.g., 64MB) for this query to keep the sort in memory. Each `work_mem` allocation is per-operation, so be careful setting it too high globally.
</details>

## Next Steps

1. If you got any quiz answers wrong, re-run the exercises above
2. Practice on **your own** databases — pick a slow query and read the plan
3. Read [Use The Index, Luke — EXPLAIN plan chapter](https://use-the-index-luke.com/sql/explain-plan)
4. When you're comfortable, move to Lesson 2: Indexing Strategies (coming next)

> [!NOTE] Ask followup questions!
> Run into a plan you can't read? Not sure why the planner chose one join over another? Ask your teacher — paste the `EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)` output and we'll dissect it together.

---

*References: [Explain Output Reference](../reference/0001-explain-output-reference.md) | Vault: [[System-Design/Databases/SQL Query Optimization]] | [[System-Design/Databases/PostgreSQL Performance Tuning]]*
