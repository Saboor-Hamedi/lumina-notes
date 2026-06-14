---
id: a1b2c3d4-1163-4000-8000-000000000163
title: Database Replication Strategies
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001163
---

**Links**: [[Database Sharding]] | [[Consistent Hashing]] | [[Database Transaction Isolation Levels]] | [[Database Transactions]] | [[Database Backup Strategies]] | [[CRDTs]]


# Database Replication Strategies

Replication copies data from one database server to others, providing redundancy, read scalability, and disaster recovery.

## Replication Topologies

### Single Leader

```
Client Writes ──► Leader ──► Replica 1
                      │          │
                      │          ▼
                      │      Read-only queries
                      │
                      └────► Replica 2
                              │
                              ▼
                          Read-only queries
```

| Pros | Cons |
|------|------|
| Simple consistency model | Write bottleneck (single leader) |
| Easy to reason about | Replica lag → stale reads |
| Widely supported (all databases) | Failover complexity |

### Multi-Leader

```
Client Writes ──► Leader A ──► Leader B ◄── Client Writes
                      │              │
                      │              │
                      ▼              ▼
                   Replica A       Replica B
```

| Pros | Cons |
|------|------|
| Low latency writes from multiple regions | Write conflicts |
| High availability (any leader can write) | Complex conflict resolution |

### Leaderless (Dynamo-style)

```
Client Writes ──► Node 1
Client Writes ──► Node 2
Client Writes ──► Node 3

Read: query all nodes, reconcile based on version
Write: write to W nodes, read from R nodes, W + R > N for quorum
```

| Pros | Cons |
|------|------|
| No single point of failure | Complex consistency |
| Low latency writes | Read repair needed |

## Synchronous vs Asynchronous

| Aspect | Synchronous | Asynchronous |
|--------|-------------|--------------|
| Durability | Leader waits for replica ack | Leader doesn't wait |
| Replica lag | Zero | Possible (seconds to minutes) |
| Write latency | Higher (network round-trip) | Lower |
| Data loss on leader failure | None | Possible (unreplicated writes lost) |
| Throughput | Lower | Higher |

```sql
-- PostgreSQL synchronous replication
-- synchronous_standby_names = 'FIRST 2 (*)'
-- Transaction commits only when 2 replicas confirm write

-- Semi-synchronous (MySQL): at least one replica confirms
-- Lossless semi-sync: replica confirms before leader commits
```

## Replication Lag Problems

### Read-Your-Writes (RYW)

```
User: Write comment → Success
User: Refresh page → Stale data (replica hasn't caught up)

Fix: Read replica from leader for user's own writes
```

### Monotonic Reads

```
User: Request 1 → Replica A (sees comment)
User: Request 2 → Replica B (lagging, doesn't see comment)

Fix: Route user to same replica (sticky sessions)
```

### Consistent Prefix Reads

```
User A: Post comment
User B: Reply to comment

If User C sees reply before original comment → confusion

Fix: Ensure related writes go to same partition
```

## Streaming Replication (PostgreSQL)

```bash
# Primary configuration (postgresql.conf)
wal_level = replica
max_wal_senders = 10
wal_keep_size = 1024

# Replica setup (pg_hba.conf on primary)
host replication replicator <replica_ip>/32 md5

# On replica (base backup + recovery.conf)
pg_basebackup -h primary -D /var/lib/postgresql/data -P -U replicator
```

**Physical replication**: Full copy of database files (WAL shipping).

**Logical replication**: Selective tables, different PostgreSQL versions.

```sql
-- Publisher (primary)
CREATE PUBLICATION my_pub FOR TABLE orders, users;

-- Subscriber (replica)
CREATE SUBSCRIPTION my_sub CONNECTION 'host=primary dbname=mydb' PUBLICATION my_pub;
```

## MySQL Group Replication

```sql
-- Group Replication (multi-leader, conflict detection)
-- Built-in group membership and failure detection
-- Certification-based replication

-- Configuration
plugin-load=group_replication.so
group_replication_group_name="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee"
group_replication_start_on_boot=off
group_replication_bootstrap_group=off
group_replication_local_address="10.0.0.1:33061"
group_replication_group_seeds="10.0.0.1:33061,10.0.0.2:33061,10.0.0.3:33061"
```

## Conflict Resolution Strategies

| Strategy | Description | When to Use |
|----------|-------------|-------------|
| Last-Writer-Wins (LWW) | Latest timestamp wins | Non-critical data |
| Merge | Combine values (e.g., CRDTs) | Collaborative data |
| Custom Conflict Handler | Application logic | Business-specific rules |
| Error on Conflict | Rollback one transaction | Strict consistency |
| Version Vectors | Track causality | Vector clocks |

## Failover

```bash
# PostgreSQL: Promote replica to primary
pg_ctl promote -D /var/lib/postgresql/data

# Or via trigger file
touch /tmp/pg_failover_trigger

# Patroni: Automated failover
patronictl list
patronictl failover <cluster-name> --master <candidate>

# After failover:
# - Old primary must be reconfigured as replica
# - Application connection string must be updated
# - WAL archiving must be redirected
```

## Monitoring Replication

```sql
-- PostgreSQL: replication status
SELECT
    application_name,
    state,
    sync_state,
    write_lag,
    flush_lag,
    replay_lag,
    pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) AS lag_bytes
FROM pg_stat_replication;

-- MySQL: replication status
SHOW REPLICA STATUS\G
-- Look for: Seconds_Behind_Master, Last_IO_Error, Last_SQL_Error
```
