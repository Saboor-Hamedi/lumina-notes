---
tags: [data-engineering, etl, fundamentals]
---

# 01 — Fundamentals

## ETL vs ELT

| Aspect | ETL | ELT |
|--------|-----|-----|
| Order | Extract → Transform → Load | Extract → Load → Transform |
| Compute | Transformation in staging | Transformation in warehouse |
| Best for | Complex transformations, legacy | Cloud warehouses (BigQuery, Snowflake) |

## Data Pipeline Stages

```
Sources → Ingestion → Storage → Transformation → Serving
  ↓           ↓          ↓           ↓              ↓
Apps,     Kafka,     Data Lake,  dbt, Spark,    BI tools,
DBs,      Kinesis,   Warehouse,  Airflow        ML models,
APIs      Airbyte    Iceberg                     reverse ETL
```

## Data Modeling

| Schema | Description | Best For |
|--------|-------------|----------|
| Star Schema | Fact + dimension tables | BI and reporting |
| Snowflake Schema | Normalized dimensions | Complex relationships |
| Data Vault | Hubs, links, satellites | Auditable, scalable DW |
| One Big Table | Denormalized flat table | ML feature stores |

## Modern Data Stack

| Layer | Tools |
|-------|-------|
| Ingestion | Airbyte, Fivetran, Debezium |
| Storage | S3, GCS, ADLS |
| Compute | Spark, Trino, DuckDB |
| Transformation | dbt, Spark SQL, Flink |
| Orchestration | Airflow, Dagster, Prefect |
| Serving | Snowflake, BigQuery, Redshift |
| BI | Tableau, Metabase, Looker |

## Fact vs Dimension Tables

- **Fact tables**: Measures (amount, quantity), foreign keys to dimensions, constantly growing
- **Dimension tables**: Descriptive attributes (name, region, category), slowly changing

## CDC (Change Data Capture)

| Method | How | Latency |
|--------|-----|---------|
| Log-based | Read DB transaction log | Real-time |
| Trigger-based | DB triggers write changes | Low |
| Timestamp-based | Query rows with updated_at | Batch |

**Links**: [[System-Design/Databases/Data Engineering/02 Architecture & Medallion]] | [[System-Design/Databases/Data Engineering/03 dbt]] | [[System-Design/Databases/Data Engineering/07 Real-Time Streaming]]
**See also**: [[ETL and Data Pipeline Patterns]], [[Data Warehouse Modeling]]
