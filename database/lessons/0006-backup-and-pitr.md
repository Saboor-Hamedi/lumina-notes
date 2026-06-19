# Lesson 6: Backup, Recovery & Point-in-Time Recovery

**Lesson 6** · ~25 min · Hands-on

> [!NOTE] Mission tie-in
> Every database administrator's worst nightmare: data loss. A proper backup strategy is non-negotiable for production systems. PostgreSQL's point-in-time recovery (PITR) lets you restore to any moment in time.

## What You'll Learn

- The difference between physical and logical backups
- How to use `pg_dump` and `pg_restore`
- Continuous archiving with WAL for PITR
- A complete backup strategy

## Backup Types

| Type | Tool | What it saves | Use for |
|------|------|---------------|---------|
| **Logical** | `pg_dump` | SQL commands to recreate data | Small DBs, selective tables, upgrades |
| **Physical** | `pg_basebackup` | Actual data files | Large DBs, full cluster recovery, PITR |
| **WAL archiving** | `archive_command` | Continuous WAL segments | Point-in-time recovery |

## Logical Backup (pg_dump)

```bash
# Dump a single database
pg_dump mydb > mydb.sql

# Dump with compression
pg_dump -Fc mydb > mydb.dump     # Custom format (compressed, parallel restore)

# Dump specific tables
pg_dump -t users -t orders mydb > users_orders.sql

# Dump all databases
pg_dumpall > all-dbs.sql

# Exclude data (schema only)
pg_dump -s mydb > schema.sql
```

### Restore

```bash
# From SQL dump
psql mydb < mydb.sql

# From custom format (parallel, more control)
pg_restore -d mydb mydb.dump
pg_restore -d mydb -j 4 mydb.dump    # 4 parallel threads
```

> [!TIP] `pg_dump -Fc` (custom format) is the best choice for most backups. It's compressed, supports parallel restore, and lets you selectively restore tables.

## Physical Backup (pg_basebackup)

```bash
# Take a full base backup
pg_basebackup -U postgres -D /backup/$(date +%Y%m%d) -Fp -P -v
```

This copies all data files. Use it for:
- Full cluster recovery
- Setting up replicas
- Point-in-time recovery (combined with WAL)

## Point-in-Time Recovery (PITR)

PITR lets you restore to any exact moment — useful for "I deleted the wrong data at 2:03 PM."

### Step 1: Enable WAL Archiving

```ini
# postgresql.conf
wal_level = replica
archive_mode = on
archive_command = 'cp %p /archive/%f'
```

This copies each WAL segment to `/archive/` after it's filled. For production, copy to S3 or a remote server.

### Step 2: Take a Base Backup

```bash
pg_basebackup -U postgres -D /backup/base_20260619 -Fp -P -v
```

### Step 3: Restore to a Point in Time

```bash
# Stop PostgreSQL, clear data directory
pg_ctl stop
rm -rf /var/lib/postgresql/data/*

# Restore base backup
cp -r /backup/base_20260619/* /var/lib/postgresql/data/

# Create restore.conf
echo "restore_command = 'cp /archive/%f %p'" >> /var/lib/postgresql/data/restore.conf
echo "recovery_target_time = '2026-06-19 14:03:00'" >> /var/lib/postgresql/data/restore.conf

# Start PostgreSQL — it will replay WAL to the target time
pg_ctl start
```

After recovery, the database is at the state it was at `14:03:00`.

## A Complete Backup Strategy

| Frequency | What | Why |
|-----------|------|-----|
| **Daily** | `pg_dump -Fc` | Quick restore of specific tables |
| **Daily** | `pg_basebackup` | Full cluster restore |
| **Continuous** | WAL archiving to S3 | Point-in-time recovery to any second |
| **Weekly** | Test restore | Verify backups actually work |

> [!WARNING] Un-tested backups are not backups. Schedule a weekly restore test to a different server. Backups that can't be restored are worthless.

## Backup Monitoring

```sql
-- Check if WAL archiving is working
SELECT * FROM pg_stat_archiver;

-- Archived WAL count and latest
SELECT archived_count, last_archived_wal, last_archived_time
FROM pg_stat_archiver;

-- Check if there's a backup lag
SELECT pg_last_xact_replay_timestamp() - now() AS recovery_lag;
```

## Hands-On

```sql
-- 1. Create a test database
CREATE DATABASE backup_test;
\c backup_test
CREATE TABLE data (id INT, value TEXT);
INSERT INTO data VALUES (1, 'important');

-- 2. Dump and restore
-- In terminal:
-- pg_dump -Fc backup_test > /tmp/backup_test.dump
-- psql -c "DROP DATABASE backup_test;"
-- pg_restore -d postgres /tmp/backup_test.dump

-- 3. Verify
\c backup_test
SELECT * FROM data;
```

## Self-Quiz

<details>
<summary><strong>1.</strong> What's the difference between <code>pg_dump</code> and <code>pg_basebackup</code>?</summary>

`pg_dump` creates a logical backup (SQL commands) of one database. `pg_basebackup` creates a physical copy of the entire cluster at the file level. Use pg_dump for single databases, pg_basebackup for full cluster + PITR.
</details>

<details>
<summary><strong>2.</strong> Why do you need WAL archiving for point-in-time recovery?</summary>

A base backup restores the database to one point in time. WAL archives contain all changes after the backup. By replaying WAL to a specific timestamp, you can restore to any moment — not just the backup time.
</details>

<details>
<summary><strong>3.</strong> What's the most important thing to test about your backup strategy?</summary>

That you can actually restore from backups. Schedule regular restore tests to a different server. A backup you can't restore is worthless.
</details>

## Next Steps

- Set up a backup schedule for any personal databases
- Test a restore in a non-production environment
- Read about [[System-Design/Databases/Database Backup Strategies]] for more strategies

> [!NOTE] Ask followup questions!
> Backup failing? Restore not working? Ask your teacher.

---

*References: Vault: [[System-Design/Databases/Database Backup Strategies]] | [[System-Design/Databases/PostgreSQL Features]]*
