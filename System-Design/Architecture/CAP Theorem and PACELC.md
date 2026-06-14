---
id: a1b2c3d4-1152-4000-8000-000000000152
title: CAP Theorem and PACELC
language: markdown
tags: [system-design, architecture, cap-theorem, distributed-systems]
selection: null
isPinned: false
timestamp: 1781500001152
---
# CAP Theorem and PACELC

The CAP theorem states that a distributed data store can only provide two of three guarantees simultaneously: Consistency, Availability, and Partition Tolerance.

## CAP Breakdown

| Property | Description |
|----------|-------------|
| **C**onsistency | Every read receives the most recent write or an error |
| **A**vailability | Every request receives a non-error response (not necessarily the latest data) |
| **P**artition Tolerance | System continues operating despite network failures |

## The CAP Choice

```
During a network partition (P), choose between:
  CP: Block writes until partition heals (sacrifice availability)
  AP: Accept stale reads (sacrifice consistency)

CP:  Availability = No (some requests timeout/error)
AP:  Consistency = No (stale data possible)
CA:  (Not realistic — partitions are inevitable in distributed systems)
```

## Systems by CAP Choice

| System | Category | CP or AP? |
|--------|----------|-----------|
| PostgreSQL (single node) | Relational | CA (no partition tolerance) |
| MongoDB | Document | CP (by default) |
| Cassandra | Wide-column | AP |
| DynamoDB | KV | AP (eventually consistent reads) |
| etcd/ZooKeeper | Coordination | CP |
| Redis | KV Cache | CP (with cluster) |
| CockroachDB | Relational (distributed) | CP |
| ScyllaDB | Wide-column | AP |

## Trade-offs in Practice

```
                ┌─────────────────────────────────────┐
                │         Consistency                  │
                │        ↑                             │
                │       / \                            │
                │      /   \                           │
                │     /     \                          │
                │    /  CP   \                         │
                │   /         \                        │
                │  /           \                       │
                │ /             \                      │
                │┌───────┐      ┌───────┐              │
                ││ RDBMS │      │ etcd  │              │
                ││       │      │       │              │
                │└───────┘      └───────┘              │
                │  ↑                                     │
                │  | Partition                           │
                │  | Tolerance                           │
                │  ↓                                     │
                │┌───────┐      ┌───────┐              │
                ││       │      │       │              │
                ││ CA    │      │ AP    │              │
                ││(single│      │       │              │
                ││ node) │      │Cass.  │              │
                │└───────┘      └───────┘              │
                │                            →           │
                │         Availability                    │
                └─────────────────────────────────────┘
```

## PACELC Extension

PACELC extends CAP: If there's a Partition (P), choose Availability or Consistency. **Else (E)**, choose Latency (L) or Consistency (C).

```
        / Partition? ── Yes ──► CP or AP (choose availability vs consistency)
       /
CAP ──┤
       \
        \ No (normal) ──► Latency vs Consistency
```

## PACELC Categories

| System | P: CP/AP | Normal: Latency/Consistency |
|--------|----------|-----------------------------|
| DynamoDB | AP | L (low latency, eventual consistency) |
| Cassandra | AP | L (low latency, tunable consistency) |
| MongoDB | CP | C (strong consistency by default) |
| CockroachDB | CP | C (strong consistency) |
| Cosmos DB | Configurable | Configurable (5 consistency levels) |

## Tunable Consistency (Cassandra)

```sql
-- Per-query consistency levels
SELECT * FROM users WHERE id = '123'
    USING CONSISTENCY QUORUM;          -- Stronger consistency

SELECT * FROM users WHERE id = '123'
    USING CONSISTENCY ONE;             -- Lower latency
```

| Level | Guarantee | Nodes Required |
|-------|-----------|---------------|
| ONE | One replica responds | Fastest, lowest consistency |
| QUORUM | Majority (N/2+1) | Balance |
| ALL | All replicas | Strongest, slowest |
| LOCAL_QUORUM | Majority in local DC | Cross-DC latency avoided |

## Real-World Implications

```
Choose AP when:
  - Uptime is critical (e-commerce, social media)
  - Stale reads are acceptable (catalog, feeds)
  - Clients can handle eventual consistency

Choose CP when:
  - Data correctness is critical (banking, inventory)
  - Stale data causes real harm (counter, balance)
  - System can tolerate brief unavailability
```

**Links**: [[System Design Fundamentals]] | [[Raft Consensus Algorithm]] | [[Consistent Hashing]] | [[Distributed Systems Design]] | [[Database Sharding]]
