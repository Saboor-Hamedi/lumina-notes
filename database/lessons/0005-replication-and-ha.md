# Lesson 5: Replication & High Availability

**Lesson 5** · ~25 min · Hands-on

> [!NOTE] Mission tie-in
> Production databases don't run on a single node. Replication gives you read scaling, failover, and disaster recovery. Understanding how PostgreSQL replicates data is essential for any production deployment.

## What You'll Learn

- Streaming replication — how the primary sends WAL to replicas
- Synchronous vs asynchronous replication
- Failover and promotion
- Logical replication for selective data sharing

## Why Replicate?

| Benefit | Description |
|---------|-------------|
| **Read scaling** | Replicas handle read queries, primary handles writes |
| **High availability** | If primary fails, a replica can be promoted |
| **Backup offloading** | Run backups on replicas, not the primary |
| **Disaster recovery** | Replica in a different datacenter/region |

## Streaming Replication

PostgreSQL's built-in streaming replication works by shipping WAL:

```
Primary                          Replica
  │                                │
  │────── WAL Stream ────────────► │  Continuous WAL shipping
  │       (changes)               │  Applied in real-time
  │                                │
  │  Writes accepted               │  Read-only queries
  │  Reads + writes                │  Reads only
```

### Setup (Primary)

```ini
# postgresql.conf
wal_level = replica
max_wal_senders = 5           # Max concurrent replicas
wal_keep_size = 1024          # MB of WAL to retain for replicas
```

```bash
# pg_hba.conf — allow replica connections
host replication replicator primary_ip/32 md5
```

### Setup (Replica)

```bash
# Take a base backup from the primary
pg_basebackup -h primary_ip -D /var/lib/postgresql/data \
  -U replicator -P -v --wal-method=stream

# Create standby signal file
touch /var/lib/postgresql/data/standby.signal

# Start PostgreSQL — it will connect to primary and stream
pg_ctl start
```

## Synchronous vs Asynchronous

| Mode | Primary commit | Data safety |
|------|---------------|-------------|
| **Async** (default) | Returns immediately | Replica may lag, potential data loss on crash |
| **Sync** | Waits for replica confirm | No data loss, but slower writes |

```ini
# Synchronous replication
synchronous_standby_names = 'FIRST 1 (replica1)'
```

## Failover

If the primary fails, promote a replica:

```bash
# On the replica
pg_ctl promote

# Or using trigger file (legacy)
touch /tmp/pg-failover-trigger
```

After promotion, the replica becomes the new primary. Other replicas need to be reconfigured to follow the new primary.

> [!TIP] Use Patroni or repmgr for automatic failover in production. Manual failover is for learning and emergencies.

## Logical Replication

Streaming replication copies the entire database cluster. Logical replication copies selected tables:

```sql
-- Publisher (primary)
CREATE PUBLICATION mypub FOR TABLE users, orders;

-- Subscriber (replica)
CREATE SUBSCRIPTION mysub CONNECTION 'host=primary_ip dbname=mydb' PUBLICATION mypub;
```

Use cases:
- Migrate specific tables between databases
- Upgrade PostgreSQL (logical replication works across versions)
- Selective data distribution

## Monitoring Replication

```sql
-- Check replica status on primary
SELECT client_addr, state, sync_state,
       pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) AS replay_lag
FROM pg_stat_replication;

-- Check replica status on standby
SELECT pg_is_in_recovery(), pg_last_wal_receive_lsn(), pg_last_wal_replay_lsn();
```

## Self-Quiz

<details>
<summary><strong>1.</strong> What data does streaming replication send from primary to replica?</summary>

The WAL (Write-Ahead Log). The primary streams WAL segments to the replica, which replays them to stay in sync.
</details>

<details>
<summary><strong>2.</strong> What's the tradeoff between synchronous and asynchronous replication?</summary>

Synchronous = safer (no data loss on failover) but slower writes (must wait for replica). Asynchronous = faster writes but potential data loss if the primary crashes before the replica receives the WAL.
</details>

<details>
<summary><strong>3.</strong> How is logical replication different from streaming replication?</summary>

Streaming replication copies the entire cluster at the WAL level. Logical replication copies selected tables at the SQL level and can work across different PostgreSQL versions.
</details>

## Next Steps

- If you have two machines/VMs, try setting up streaming replication
- Monitor replication lag in your actual databases
- Read about Patroni for automatic HA in production

> [!NOTE] Ask followup questions!
> Replication stuck? Lag too high? Want to set up HA? Ask your teacher.

---

*References: Vault: [[System-Design/Databases/Database Replication Strategies]] | [[System-Design/Databases/PostgreSQL Features]]*
