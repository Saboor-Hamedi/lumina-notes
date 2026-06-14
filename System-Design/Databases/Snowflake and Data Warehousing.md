---
id: a1b2c3d4-1113-4000-8000-000000000113
title: Snowflake and Data Warehousing
language: markdown
tags: [system-design, databases, snowflake, data-warehouse]
selection: null
isPinned: false
timestamp: 1781500001113
---

**Links**: [[Data Warehouse Modeling]] | [[ClickHouse]] | [[Database Engines Compared]] | [[DuckDB]] | [[ETL and Data Pipeline Patterns]] | [[Delta Lake and Apache Iceberg]]


# Snowflake and Data Warehousing

Snowflake is a cloud-native data warehouse that separates compute from storage, allowing independent scaling. It supports structured and semi-structured data with standard SQL.

## Architecture

```
┌─────────────────────────────────────────────┐
│            Cloud Services Layer              │
│  Authentication, Query optimization, Metadata│
│  Infrastructure management, Access control  │
├─────────────────────────────────────────────┤
│              Compute Layer (Virtual Warehouse)│
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │ X-Small  │  │ Small    │  │ Medium   │  │
│  │ (1 node) │  │ (2 nodes)│  │ (4 nodes)│  │
│  └──────────┘  └──────────┘  └──────────┘  │
├─────────────────────────────────────────────┤
│              Storage Layer (S3/GCS/Azure)    │
│  Compressed, columnar, immutable files       │
└─────────────────────────────────────────────┘
```

| Layer | Scales | Cost |
|-------|--------|------|
| Storage | Automatically (compressed) | Per TB stored |
| Compute | Manually (warehouse size) | Per second while running |
| Services | Automatically | Included |

## Key Features

| Feature | Benefit |
|---------|---------|
| Separation of compute/storage | Scale independently, zero overhead |
| Cloning (zero-copy) | Instant database snapshots, no extra storage |
| Time Travel (up to 90 days) | Query/restore historical data |
| Data Sharing | Share live data across Snowflake accounts |
| Semi-structured data | Native VARIANT type (JSON, Avro, Parquet, XML) |
| Automatic clustering | No manual index maintenance |
| Tasks & Streams | Built-in pipelines (CDC + scheduled jobs) |

## SQL Example

```sql
-- Create warehouse
CREATE WAREHOUSE analytics_wh WITH
    WAREHOUSE_SIZE = 'MEDIUM'
    AUTO_SUSPEND = 300
    AUTO_RESUME = TRUE;

-- Create table with clustering
CREATE TABLE orders (
    order_id VARCHAR(36),
    user_id VARCHAR(36),
    amount DECIMAL(10,2),
    created_at TIMESTAMP_NTZ,
    metadata VARIANT  -- semi-structured
)
CLUSTER BY (created_at, user_id);

-- Query historical data (time travel)
SELECT * FROM orders
    AT (TIMESTAMP => '2026-06-10 12:00:00'::TIMESTAMP);

-- Clone without copying data
CREATE DATABASE dev_analytics CLONE prod_analytics;

-- Stream for CDC
CREATE STREAM order_changes ON TABLE orders;
```

## Virtual Warehouse Sizing

| Size | Nodes | Use Case |
|------|-------|----------|
| X-Small | 1 | Dev, light queries |
| Small | 2 | Reporting, small ETL |
| Medium | 4 | BI dashboards |
| Large | 8 | Heavy ETL, complex queries |
| X-Large | 16 | Large-scale analytics |
| 2X-Large+ | 32+ | Massive data processing |

- Auto-scaling: Add clusters automatically when queue builds up
- Multi-cluster: Up to 10 concurrent clusters for high concurrency
- Auto-suspend: Stop warehouse after idle period (save cost)

## Materialized Views

```sql
CREATE MATERIALIZED VIEW daily_sales AS
SELECT
    DATE_TRUNC('day', created_at) AS day,
    COUNT(*) AS order_count,
    SUM(amount) AS total_revenue
FROM orders
GROUP BY 1;
```

Automatically maintained — no manual refresh.

## Zero-Copy Cloning

```sql
-- Instant clone (no data copy)
CREATE DATABASE analytics_sandbox CLONE production;

CREATE SCHEMA experiment CLONE analytics.dbt_schema;
```

- Metadata-only operation (seconds, not hours)
- Storage shared until data is modified
- Used for dev/testing, snapshots, data sharing

## Loading Data

```sql
-- Internal stage
PUT file://data/orders.csv @my_stage;

COPY INTO orders
FROM @my_stage/orders.csv
FILE_FORMAT = (TYPE = CSV, SKIP_HEADER = 1)
ON_ERROR = 'CONTINUE';

-- External stage (S3)
CREATE STAGE s3_stage
    URL = 's3://my-bucket/data/'
    CREDENTIALS = (AWS_KEY_ID = '...' AWS_SECRET_KEY = '...');

-- Snowpipe (auto-ingest)
CREATE PIPE order_pipe
    AUTO_INGEST = TRUE
    AS COPY INTO orders FROM @s3_stage;
```

**Links**: [[Data Engineering]] | [[Apache Spark]] | [[SQL vs NoSQL Databases]] | [[Cloud Computing]] | [[Time Series Databases]]
