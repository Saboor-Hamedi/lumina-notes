---
id: 0001-explain-output-reference
title: 0001-explain-output-reference
language: markdown
tags: ''
selection: null
isPinned: false
customIcon: null
timestamp: 1781870832130
---
# Explain Output Reference

**Reference** · Quick lookup

## EXPLAIN Variants

| Command | What it adds |
|---------|--------------|
| `EXPLAIN` | Estimated plan only — no execution |
| `EXPLAIN ANALYZE` | Executes the query and shows actual vs estimated |
| `EXPLAIN (ANALYZE, BUFFERS)` | Adds buffer hit/miss counts per node |
| `EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)` | Machine-readable JSON output with all details |
| `EXPLAIN (ANALYZE, BUFFERS, FORMAT YAML)` | Human-readable structured output |

## Cost Components

| Setting | Default | Meaning |
|---------|---------|---------|
| `seq_page_cost` | 1.0 | Cost of reading a page sequentially |
| `random_page_cost` | 4.0 (HDD) / 1.1 (SSD) | Cost of random page access — **tune this for SSD!** |
| `cpu_tuple_cost` | 0.01 | Cost of processing a row |
| `cpu_index_tuple_cost` | 0.005 | Cost of processing an index entry |
| `cpu_operator_cost` | 0.0025 | Cost of a WHERE clause or operator |

## Common Scan Nodes

| Node | Pattern | Good when |
|------|---------|-----------|
| `Seq Scan` | Reads entire table sequentially | Table is small, or query reads >5-10% of rows |
| `Index Scan` | B-tree walk + heap fetch | Selective query (few rows returned) |
| `Index Only Scan` | All data in index, no heap visit | Index covers the query (all needed columns are in the index) |
| `Bitmap Index Scan` → `Bitmap Heap Scan` | Build page bitmap from index, then fetch pages | Many rows but not enough for Seq Scan; rechecks per-page filters |

## Common Join Nodes

| Node | Algorithm | Best for |
|------|-----------|----------|
| `Nested Loop` | For each outer row, scan inner once | Small outer + indexed inner |
| `Hash Join` | Build hash on inner, probe with outer | Medium tables, no ordering needed |
| `Merge Join` | Walk both sorted inputs in parallel | Large pre-sorted inputs (e.g., from indexes) |

## Useful Diagnostic Queries

**Top queries by total time (requires pg_stat_statements):**

```sql
SELECT
  query,
  calls,
  total_exec_time / 1000 AS total_seconds,
  mean_exec_time,
  rows,
  shared_blks_hit,
  shared_blks_read,
  (100 * shared_blks_hit / NULLIF(shared_blks_hit + shared_blks_read, 0))::INT AS hit_pct
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 10;
```

**Queries with most I/O:**

```sql
SELECT query, calls, shared_blks_read, local_blks_read, temp_blks_read
FROM pg_stat_statements
ORDER BY shared_blks_read DESC
LIMIT 10;
```

**Cache hit ratio:**

```sql
SELECT
  SUM(heap_blks_hit) / NULLIF(SUM(heap_blks_hit) + SUM(heap_blks_read), 0) * 100 AS cache_hit_pct
FROM pg_statio_user_tables;
```

## Reading Plan Output

Rules of thumb:

1. Read plans **inside-out** and **bottom-up** — the most indented nodes run first
2. **Actual vs estimated rows**: mismatch >10x means stale stats (run `ANALYZE`)
3. **Cost** is in arbitrary units — only compare costs within the same plan
4. **Node with highest total cost** is the bottleneck — focus your optimization there
5. `BUFFERS` output: `shared_hit` = found in cache, `shared_read` = disk read

---

[← Back to Lesson 1](../lessons/0001-reading-query-plans.md)
