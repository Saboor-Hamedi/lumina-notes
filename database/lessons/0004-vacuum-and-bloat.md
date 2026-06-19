# Lesson 4: VACUUM & Bloat Deep Dive

**Lesson 4** · ~25 min · Hands-on

> [!NOTE] Mission tie-in
> Every PostgreSQL database accumulates bloat over time. Left unchecked, it slows queries, wastes disk, and can cause catastrophic "transaction ID wraparound." `VACUUM` is the maintenance that keeps PostgreSQL healthy. Understanding it is essential for production database management.

## What You'll Learn

- Why VACUUM exists (MVCC cleanup)
- The difference between `VACUUM`, `VACUUM FULL`, and `ANALYZE`
- How to monitor bloat and tune autovacuum
- What transaction ID wraparound is and why it matters

## Why Vacuum?

From Lesson 3: every `UPDATE` and `DELETE` creates dead tuples. VACUUM's job is to:

1. **Mark dead tuple space as reusable** — so new rows can reuse the disk space
2. **Update the visibility map** — enables Index Only Scans
3. **Freeze old transaction IDs** — prevents wraparound (critical!)

## VACUUM Variants

| Command | What it does | When to use |
|---------|-------------|-------------|
| `VACUUM` | Marks dead space as reusable, doesn't shrink file size | Regular maintenance |
| `VACUUM ANALYZE` | Same as above + updates table statistics | After significant data changes |
| `VACUUM FULL` | Rewrites entire table, shrinks file size, takes exclusive lock | Off-peak, when you need disk space back |
| `VACUUM (VERBOSE, ANALYZE)` | Shows detailed per-table report | Debugging bloat issues |

```sql
-- Standard maintenance
VACUUM ANALYZE;

-- Check what vacuum actually did
VACUUM (VERBOSE, ANALYZE) orders;

-- Aggressive — only during maintenance window
VACUUM FULL orders;
```

> [!WARNING] `VACUUM FULL` takes an `ACCESS EXCLUSIVE` lock — the table is completely unavailable while it runs. Never run this on a production table during business hours.

## Autovacuum

PostgreSQL runs VACUUM automatically. These settings control it:

```ini
# postgresql.conf
autovacuum = on                          # Should always be on
autovacuum_max_workers = 4               # Parallel workers
autovacuum_naptime = 1min                # Check interval
autovacuum_vacuum_threshold = 50         # Minimum dead tuples before vacuum
autovacuum_vacuum_scale_factor = 0.01    # Additional threshold: 1% of table
```

A table gets vacuumed when: `dead_tuples > threshold + (scale_factor * total_rows)`

For a 1M row table: `50 + (0.01 * 1M) = 10,050` dead tuples triggers vacuum.

### Per-Table Tuning

Heavy-write tables need more aggressive vacuuming:

```sql
ALTER TABLE orders SET (autovacuum_vacuum_scale_factor = 0.005);
ALTER TABLE orders SET (autovacuum_vacuum_threshold = 10000);
ALTER TABLE orders SET (autovacuum_vacuum_cost_limit = 1000);
```

## Transaction ID Wraparound

Every transaction gets a 32-bit ID (max ~4 billion). When a row's `xmin` is "before" the current transaction ID, PostgreSQL can't tell if it's visible. To fix this, PostgreSQL "freezes" old rows — marking them as always visible.

If autovacuum doesn't keep up, you risk **wraparound** — PostgreSQL will shut down to prevent data corruption. This is an emergency.

```sql
-- Check wraparound risk
SELECT datname, age(datfrozenxid) AS xid_age,
       round(100 * age(datfrozenxid) / 2000000000.0, 2) AS xid_pct_used
FROM pg_database
ORDER BY xid_age DESC;
```

> [!WARNING] If `xid_pct_used` exceeds 80%, your autovacuum isn't keeping up. At 100%, the database will stop accepting writes. At ~150-200M (about 10%), you should start monitoring.

## Monitoring Bloat

```sql
-- Table bloat (approximate)
SELECT schemaname, tablename, n_dead_tup, n_live_tup,
       round(n_dead_tup * 100.0 / NULLIF(n_live_tup + n_dead_tup, 0), 1) AS dead_pct
FROM pg_stat_user_tables
WHERE n_live_tup > 0 AND n_dead_tup > 1000
ORDER BY dead_pct DESC
LIMIT 10;

-- Last vacuum time per table
SELECT schemaname, tablename, last_vacuum, last_autovacuum, last_analyze
FROM pg_stat_user_tables
ORDER BY last_autovacuum DESC NULLS LAST;

-- Indexes that haven't been used
SELECT schemaname, tablename, indexname, idx_scan
FROM pg_stat_user_indexes
ORDER BY idx_scan ASC
LIMIT 20;
```

## Hands-On

```sql
-- 1. Check current bloat
SELECT schemaname, tablename, n_dead_tup, n_live_tup,
       round(n_dead_tup * 100.0 / NULLIF(n_live_tup, 0), 1) AS dead_pct
FROM pg_stat_user_tables
WHERE n_live_tup > 0
ORDER BY dead_pct DESC;

-- 2. Run vacuum with verbose output
VACUUM (VERBOSE, ANALYZE) users;
VACUUM (VERBOSE, ANALYZE) orders;

-- 3. Generate some bloat on purpose
BEGIN;
UPDATE users SET name = name || '.';
-- Creates dead tuples!
ROLLBACK;

-- 4. See the bloat appear
SELECT n_dead_tup FROM pg_stat_user_tables WHERE tablename = 'users';

-- 5. Let autovacuum clean it (wait a minute or force it)
VACUUM users;
```

## Self-Quiz

<details>
<summary><strong>1.</strong> What's the difference between <code>VACUUM</code> and <code>VACUUM FULL</code>?</summary>

`VACUUM` marks dead space as reusable but doesn't shrink the file. `VACUUM FULL` rewrites the table to reclaim disk space but takes an exclusive lock — table is unavailable while it runs.
</details>

<details>
<summary><strong>2.</strong> What triggers autovacuum to run on a table?</summary>

When `n_dead_tup > autovacuum_vacuum_threshold + (autovacuum_vacuum_scale_factor * total_rows)`. Default: `50 + (0.01 * total_rows)`.
</details>

<details>
<summary><strong>3.</strong> What happens if transaction ID wraparound isn't prevented?</summary>

PostgreSQL will shut down the database to prevent data corruption. It requires single-user mode to recover. This is a production emergency — always monitor `age(datfrozenxid)`.
</details>

## Next Steps

- Check bloat on your real databases with the queries above
- Tune autovacuum for any heavy-write tables you manage
- Review your `pganalyze` or `pg_stat_statements` for signs of bloat-related slowdowns
- Continue to Lesson 5: Replication & High Availability (coming next)

> [!NOTE] Ask followup questions!
> Seeing high bloat and not sure how to fix it? Ask your teacher — we'll make a plan.

---

*References: Vault: [[System-Design/Databases/PostgreSQL Features]] | [[System-Design/Databases/PostgreSQL Performance Tuning]]*
