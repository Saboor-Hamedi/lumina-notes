---
id: a1b2c3d4-1114-4000-8000-000000000114
title: ClickHouse
language: markdown
tags: [system-design, databases, clickhouse, olap]
selection: null
isPinned: false
timestamp: 1781500001114
---

**Links**: [[Snowflake and Data Warehousing]] | [[Database Engines Compared]] | [[Data Warehouse Modeling]] | [[SQL vs NoSQL Databases]] | [[DuckDB]] | [[Time Series Databases]]


# ClickHouse

ClickHouse is a column-oriented DBMS for online analytical processing (OLAP) that enables real-time queries on petabyte-scale datasets.

## Column-Oriented Storage

```
Row-oriented (PostgreSQL):
┌─────┬───────┬───────┐
│ id  │ name  │ city  │
├─────┼───────┼───────┤
│ 1   │ Alice │ NY    │
│ 2   │ Bob   │ SF    │
│ 3   │ Carol │ NY    │
└─────┴───────┴───────┘

Column-oriented (ClickHouse):
id:      [1,        2,       3]
name:    ["Alice",  "Bob",  "Carol"]
city:    ["NY",     "SF",   "NY"]
```

**Advantage for analytics**: Only read the columns you need. City column = 3 values vs 9 cells.

## Key Features

| Feature | Benefit |
|---------|---------|
| Columnar storage | 10-100x compression, read only needed columns |
| Vectorized query execution | SIMD-optimized, billions rows/second |
| MergeTree engine | Append-optimized, background merges |
| Materialized views | Pre-computed aggregations, auto-update |
| Distributed queries | Transparent multi-node execution |
| SQL dialect | Familiar with analytical extensions |

## Table Engines

```sql
-- MergeTree (primary storage engine)
CREATE TABLE events (
    event_date Date,
    event_time DateTime,
    user_id UInt64,
    event_type String,
    amount Float64
) ENGINE = MergeTree
ORDER BY (event_date, event_type)
PARTITION BY toYYYYMM(event_date)
TTL event_date + INTERVAL 90 DAY DELETE;

-- ReplacingMergeTree (deduplicates on merge)
CREATE TABLE users (
    user_id UInt64,
    name String,
    updated_at DateTime
) ENGINE = ReplacingMergeTree(updated_at)
ORDER BY user_id;

-- SummingMergeTree (pre-aggregates numeric columns)
CREATE TABLE page_views (
    page String,
    date Date,
    views UInt64
) ENGINE = SummingMergeTree()
ORDER BY (date, page);
```

## Analytical Queries

```sql
-- Billion-row aggregation in milliseconds
SELECT
    event_type,
    COUNT() AS events,
    COUNT(DISTINCT user_id) AS unique_users,
    AVG(amount) AS avg_amount,
    quantile(0.95)(amount) AS p95_amount
FROM events
WHERE event_date BETWEEN '2026-01-01' AND '2026-06-13'
GROUP BY event_type
ORDER BY events DESC
LIMIT 10;

-- Approximate counting (HyperLogLog)
SELECT uniq(user_id) FROM events WHERE event_date = today();

-- Funnel analysis
SELECT
    level,
    COUNT() AS users
FROM (
    SELECT user_id, windowFunnel(3600)(
        event_time,
        event_type = 'page_view',
        event_type = 'add_to_cart',
        event_type = 'purchase'
    ) AS level
    FROM events
    WHERE event_date = yesterday()
    GROUP BY user_id
)
GROUP BY level;
```

## Performance Characteristics

| Metric | Performance |
|--------|-------------|
| Single query throughput | 100M-1B rows/sec per node |
| INSERT speed | 10M-50M rows/sec |
| Compression ratio | 5-15x (vs raw, depending on column type) |
| Query latency (hot data) | < 10ms |
| Query latency (cold data) | < 100ms |

## Distributed Tables

```sql
-- Create local tables on each node
CREATE TABLE events_local ON CLUSTER cluster (
    event_date Date,
    event_type String,
    user_id UInt64
) ENGINE = ReplicatedMergeTree('/clickhouse/tables/{shard}/events', '{replica}')
ORDER BY (event_date, event_type);

-- Create distributed view
CREATE TABLE events_distributed AS events_local
ENGINE = Distributed('cluster', 'default', 'events_local', rand());
```

## Data Ingestion

```sql
-- From file
INSERT INTO events FORMAT CSV
  '2026-06-13','click',12345
  '2026-06-13','purchase',67890

-- From Kafka
CREATE TABLE events_queue AS events
ENGINE = Kafka('kafka:9092', 'events', 'clickhouse', 'JSONEachRow');

-- Materialized view (Kafka → MergeTree)
CREATE MATERIALIZED VIEW events_mv TO events AS
SELECT * FROM events_queue;
```

## Use Cases

| Use Case | Why ClickHouse |
|----------|----------------|
| Real-time analytics | Sub-second queries on billions of rows |
| Observability (logs, traces) | High ingest + fast search |
| Product analytics | User behavior, funnels, retention |
| Financial data | Time-series aggregations |
| Ad tech / RTB | Real-time counting, filtering |

## Limitations

- No row-level transactions (no UPDATE/DELETE without mutation)
- Not suited for point lookups (use PostgreSQL/MySQL)
- Limited JOIN performance (use dictionary for lookup tables)
- Single-row insert is slow (batch inserts recommended)

**Links**: [[Snowflake and Data Warehousing]] | [[Time Series Databases]] | [[Apache Spark]] | [[Data Engineering]] | [[SQL Query Optimization]]
