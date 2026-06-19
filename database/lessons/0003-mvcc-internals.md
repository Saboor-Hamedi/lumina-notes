# Lesson 3: MVCC Internals

**Lesson 3** · ~25 min · Hands-on

> [!NOTE] Mission tie-in
> MVCC (Multi-Version Concurrency Control) is why PostgreSQL can handle thousands of concurrent readers and writers without locking each other out. Understanding MVCC explains half of PostgreSQL's behavior: why `VACUUM` exists, why `UPDATE` is actually a delete+insert, and what "snapshot isolation" really means.

## What You'll Learn

- How MVCC makes reads never block writes
- What dead tuples are and where they come from
- How transaction IDs (xmin/xmax) work
- How snapshot isolation prevents dirty/phantom reads

## The Core Idea

In PostgreSQL, **readers never block writers, and writers never block readers.**

This is achieved by keeping multiple versions of each row. When a transaction modifies a row, PostgreSQL creates a new version rather than overwriting the old one. Each transaction sees the version that was current when it started.

## xmin and xmax

Every row has two hidden system columns:

| Column | Meaning |
|--------|---------|
| `xmin` | The transaction ID that created this row version |
| `xmax` | The transaction ID that deleted/obsoleted this row version (0 if still visible) |

```sql
SELECT xmin, xmax, *, ctid FROM users LIMIT 3;
```

- A row is **visible** to your transaction if `xmin` is committed and `xmax` is 0 (or not yet committed)
- An `UPDATE` sets `xmax` on the old version and creates a new version with `xmin`
- A `DELETE` sets `xmax` on the current version

## Snapshot Isolation

When a transaction starts, PostgreSQL takes a **snapshot** — the set of in-progress transactions at that moment.

| Isolation Level | What it prevents | How |
|----------------|-----------------|-----|
| `READ COMMITTED` (default) | Dirty reads | Each query sees only committed data |
| `REPEATABLE READ` | Dirty + non-repeatable reads | All queries see the same snapshot |
| `SERIALIZABLE` | Dirty + non-repeatable + phantoms | Detects serialization conflicts |

```sql
-- See current isolation level
SHOW transaction_isolation;

-- Change for current transaction
BEGIN;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SELECT * FROM users;  -- Sees a fixed snapshot
COMMIT;
```

### PostgreSQL vs SQL Standard

PostgreSQL goes beyond the standard:
- `READ COMMITTED` prevents dirty reads (as required)
- `REPEATABLE READ` also prevents phantom reads (SQL standard allows them)
- `SERIALIZABLE` uses Serializable Snapshot Isolation (SSI) — detects conflicts that could break serial order

## Dead Tuples

When a row is updated or deleted, the old version becomes a **dead tuple**. It's still on disk, but no active transaction can see it.

```sql
-- Check dead tuple count
SELECT schemaname, tablename, n_dead_tup, n_live_tup,
       round(n_dead_tup * 100.0 / NULLIF(n_live_tup, 0), 1) AS dead_pct
FROM pg_stat_user_tables
ORDER BY dead_pct DESC;
```

A high dead tuple percentage (>20%) means:
- `VACUUM` isn't keeping up
- Queries may scan more pages than needed
- Index bloat is likely

## Hands-On: Watch MVCC in Action

```sql
-- Terminal 1: Create and inspect
CREATE TABLE mvcc_test (id INT, val TEXT);
INSERT INTO mvcc_test VALUES (1, 'hello');
SELECT xmin, xmax, ctid, * FROM mvcc_test;

-- Terminal 2: Update in another transaction
BEGIN;
UPDATE mvcc_test SET val = 'world' WHERE id = 1;
-- Don't commit yet!

-- Terminal 1 (separate session): See the old version
SELECT xmin, xmax, ctid, * FROM mvcc_test;
-- Still sees 'hello' — snapshot isolation

-- Terminal 2: Commit
COMMIT;

-- Terminal 1: Now see the new version
SELECT xmin, xmax, ctid, * FROM mvcc_test;
-- Now sees 'world' — new snapshot

-- Check dead tuples
SELECT n_dead_tup FROM pg_stat_user_tables WHERE tablename = 'mvcc_test';

-- Cleanup
DROP TABLE mvcc_test;
```

## Hands-On: Isolation Levels

```sql
-- Terminal 1
BEGIN;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SELECT * FROM users WHERE id = 1;

-- Terminal 2 (separate session)
UPDATE users SET name = 'Changed' WHERE id = 1;
COMMIT;

-- Terminal 1 — still sees the old name (REPEATABLE READ snapshot)
SELECT * FROM users WHERE id = 1;
COMMIT;

-- After commit — now sees the change
SELECT * FROM users WHERE id = 1;
```

## Self-Quiz

<details>
<summary><strong>1.</strong> What is a dead tuple?</summary>

An old version of a row that's no longer visible to any active transaction. Created by `UPDATE` and `DELETE`. `VACUUM` removes them.
</details>

<details>
<summary><strong>2.</strong> If `xmax` is 0 on a row, what does that mean?</summary>

The row has not been deleted or updated — it's the current live version. If `xmax` is set to a transaction ID, that transaction deleted or obsoleted this version.
</details>

<details>
<summary><strong>3.</strong> In PostgreSQL, does `REPEATABLE READ` prevent phantom reads?</summary>

Yes — PostgreSQL's `REPEATABLE READ` prevents phantoms (exceeding the SQL standard). It uses MVCC snapshots so all queries in the transaction see the same data, even if other transactions insert new rows.
</details>

## Next Steps

- Try the isolation levels experiment above with a friend or two terminal windows
- Check your actual databases for dead tuple percentage
- Move to [Lesson 4: VACUUM & Bloat Deep Dive](0004-vacuum-and-bloat.md)

> [!NOTE] Ask followup questions!
> Confused about which isolation level to use? Seeing "could not serialize access" errors? Ask your teacher.

---

*References: Vault: [[System-Design/Databases/Database Transaction Isolation Levels]] | [[System-Design/Databases/PostgreSQL Features]]*
