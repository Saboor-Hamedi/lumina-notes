---
id: dev-058-0000-0000-0000-000000000043
title: Database Backup Strategies
language: markdown
tags:
  - system-design
  - databases
  - backup
  - disaster-recovery
selection: null
isPinned: false
timestamp: 1781825330377
---
# Database Backup Strategies


**Links**: [[PostgreSQL Features]] | [[Database Security]] | [[Cloud Computing]] | [[Infrastructure as Code]] | [[Disaster Recovery]]

## Why Backup?

| Threat | Impact |
|--------|--------|
| Hardware failure | Data loss |
| Human error (DROP TABLE) | Data corruption |
| Security breach | Ransomware, data theft |
| Software bug | Corrupted writes |
| Natural disaster | Site outage |

## Backup Types

| Type | Description | Recovery Speed | Storage |
|------|-------------|----------------|---------|
| **Full** | Complete copy | Fastest | Largest |
| **Incremental** | Changes since last backup | Medium | Small |
| **Differential** | Changes since last full | Fast | Medium |
| **Continuous Archiving** | WAL archive | Point-in-time | Medium |

## PostgreSQL Backup

### pg_dump (Logical Backup)

```bash
# Single database
pg_dump -h localhost -U postgres -F c mydb > mydb.dump

# Restore
pg_restore -h localhost -U postgres -d mydb mydb.dump

# Compressed
pg_dump -Z 9 mydb | gzip > mydb.sql.gz
```

### Physical Backup (pg_basebackup)

```bash
# Full physical backup (for replication/PITR)
pg_basebackup -h localhost -D /backup/dir -X stream -P

# Restore: copy to data directory, create recovery.conf
```

### Point-in-Time Recovery (PITR)

```sql
-- postgresql.conf
wal_level = replica
archive_mode = on
archive_command = 'cp %p /backup/wal/%f'
```

```bash
# Restore to specific time
pg_restore --target-time "2024-01-15 14:30:00 UTC"
```

## Automation

```bash
#!/bin/bash
# Daily backup script
BACKUP_DIR="/backup/$(date +%Y-%m-%d)"
mkdir -p $BACKUP_DIR
pg_dump -F c mydb > $BACKUP_DIR/mydb.dump
aws s3 cp $BACKUP_DIR/mydb.dump s3://my-backups/
```

## 3-2-1 Rule

- **3** copies of data
- **2** different storage types
- **1** copy off-site

**Next**: [[PostgreSQL Extensions]] — Extending PostgreSQL
