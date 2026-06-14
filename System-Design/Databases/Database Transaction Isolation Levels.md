---
id: a1b2c3d4-1162-4000-8000-000000000162
title: Database Transaction Isolation Levels
language: markdown
tags: [system-design, databases, transactions, isolation]
selection: null
isPinned: false
timestamp: 1781500001162
---

**Links**: [[Database Transactions]] | [[Database Replication Strategies]] | [[Database Engines Compared]] | [[PostgreSQL Features]] | [[SQL Query Optimization]] | [[CRDTs]]


# Database Transaction Isolation Levels

Isolation levels control how concurrent transactions interact. Higher isolation prevents anomalies but reduces concurrency.

## Concurrency Anomalies

| Anomaly | Description | Example |
|---------|-------------|---------|
| Dirty Read | Read uncommitted data | T1 writes, T2 reads before T1 commits |
| Non-Repeatable Read | Same row, different results | T1 reads, T2 updates, T1 reads again |
| Phantom Read | New rows appear in range queries | T1 reads range, T2 inserts, T1 re-reads |
| Lost Update | Overwrite without seeing change | T1 and T2 read same value, both write |
| Write Skew | Concurrent writes violate constraint | T1 reads x, T2 reads x, both write based on x |

## SQL Standard Isolation Levels

| Level | Dirty Read | Non-Repeatable Read | Phantom Read |
|-------|------------|---------------------|--------------|
| READ UNCOMMITTED | Possible | Possible | Possible |
| READ COMMITTED | Prevented | Possible | Possible |
| REPEATABLE READ | Prevented | Prevented | Possible |
| SERIALIZABLE | Prevented | Prevented | Prevented |

## READ UNCOMMITTED

Allows dirty reads. Rarely used in practice.

```sql
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
```

```python
# PostgreSQL doesn't support READ UNCOMMITTED (maps to READ COMMITTED)
# SQL Server, MySQL (with InnoDB) support it
```

## READ COMMITTED (Default in PostgreSQL, SQL Server, Oracle)

Each statement sees only committed data at the time it executes.

```sql
-- Transaction A
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;  -- Not yet committed

-- Transaction B
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
SELECT balance FROM accounts WHERE id = 1;  -- Sees OLD balance (A not committed)

-- Transaction A
COMMIT;

-- Transaction B (same transaction, NEW statement)
SELECT balance FROM accounts WHERE id = 1;  -- Sees NEW balance (A committed)
```

**Problem**: Non-repeatable read — same transaction reads different values.

## REPEATABLE READ

Consistent snapshot for all reads in the transaction. Prevents dirty reads and non-repeatable reads.

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- Transaction A
BEGIN;
SELECT balance FROM accounts WHERE id = 1;  -- Reads 1000

-- Transaction B (concurrent)
UPDATE accounts SET balance = 500 WHERE id = 1;  -- Succeeds

-- Transaction A
SELECT balance FROM accounts WHERE id = 1;  -- Still 1000 (snapshot)
COMMIT;
```

**PostgreSQL note**: REPEATABLE READ also prevents phantom reads (MVCC snapshot). This exceeds the SQL standard.

**Problem**: Phantom reads still possible in standard SQL (PG prevents them). Also: serialization failures on UPDATE/DELETE.

## SERIALIZABLE

Transactions execute as if running serially. The highest isolation level.

```python
# PostgreSQL SERIALIZABLE
connection.set_isolation_level(psycopg2.extensions.ISOLATION_LEVEL_SERIALIZABLE)

with connection.cursor() as cur:
    try:
        # Any serialization conflict raises:
        # ERROR: could not serialize access due to read/write dependencies
        cur.execute("...")
        connection.commit()
    except psycopg2.errors.SerializationFailure:
        # Retry the entire transaction
        retry()
```

**Implementation difference**:
- PostgreSQL: Serializable Snapshot Isolation (SSI) — detects conflicts
- MySQL/InnoDB: Locking — rows are locked to prevent conflicts
- Oracle: SERIALIZABLE = REPEATABLE READ (snapshot-based)

## Locking Mechanisms

### Pessimistic Locking (Lock rows at read time)

```sql
-- PostgreSQL
BEGIN;
SELECT * FROM accounts WHERE id = 1 FOR UPDATE;  -- Lock row

-- Other transactions trying to UPDATE this row will wait

-- FOR NO KEY UPDATE (weaker lock, allows key changes)
SELECT * FROM accounts WHERE id = 1 FOR NO KEY UPDATE;

-- FOR SHARE (shared lock, prevents UPDATE, allows read)
SELECT * FROM accounts WHERE id = 1 FOR SHARE;

-- FOR KEY SHARE (weakest)
SELECT * FROM accounts WHERE id = 1 FOR KEY SHARE;
```

### Optimistic Locking (Check at write time)

```sql
-- Using version column
UPDATE accounts
SET balance = 900, version = version + 1
WHERE id = 1 AND version = 1;

-- If version mismatch → zero rows updated → conflict → retry
```

## Snapshot Isolation (MVCC)

Most modern databases use MVCC (Multi-Version Concurrency Control):

```
Each transaction sees a snapshot of data at its start time.

Row: (value, created_by_txn, deleted_by_txn)

- Writer creates a new version
- Reader always sees the version that was current at snapshot time
- Deleted rows are marked (not physically removed until VACUUM)
```

## Isolation Level by Database

| Database | Default | Maximum |
|----------|---------|---------|
| PostgreSQL | READ COMMITTED | SERIALIZABLE |
| MySQL (InnoDB) | REPEATABLE READ | SERIALIZABLE |
| SQL Server | READ COMMITTED | SERIALIZABLE |
| Oracle | READ COMMITTED | SERIALIZABLE |
| SQLite | SERIALIZABLE | SERIALIZABLE |
| CockroachDB | SERIALIZABLE | SERIALIZABLE |

## Choosing an Isolation Level

| Use Case | Recommended Level |
|----------|------------------|
| Reporting, analytics | READ COMMITTED |
| Financial transactions | SERIALIZABLE |
| Inventory management | REPEATABLE READ |
| Blog comments | READ COMMITTED |
| Account balance transfers | SERIALIZABLE |
| Caching layer | READ UNCOMMITTED (rare) |
