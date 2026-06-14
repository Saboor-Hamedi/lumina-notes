---
tags: [data-engineering, reference, cheatsheet]
---

# 10 — Quick Reference

| Category | Tool | Use Case | Pros | Cons |
|----------|------|----------|------|------|
| Ingestion | Airbyte | SaaS → warehouse | 300+ connectors, open source | Python-heavy custom connectors |
| Ingestion | Fivetran | Managed ingestion | Zero maintenance | Expensive at scale |
| Ingestion | Debezium | CDC from DBs | Kafka-native, reliable | Complex setup |
| Streaming | Kafka | Event backbone | Battle-tested, huge ecosystem | Operational complexity |
| Streaming | Flink | Real-time processing | True streaming, event-time | Learning curve |
| Compute | Spark | Large-scale ETL | Mature, batch+streaming | Memory-heavy |
| Compute | DuckDB | Analytical queries | Embeddable, fast, free | Single-node |
| Transform | dbt | SQL transformations | Version control, testing | SQL only |
| Orchestration | Airflow | Complex DAGs | Extensible, huge community | Ops-heavy |
| Orchestration | Dagster | Data platform | Asset-centric, testing | Smaller community |
| Orchestration | Prefect | Python-first flows | Great DX, auto-retry | Cloud for best features |
| Storage | Iceberg | Lakehouse format | ACID, time travel, schema evolution | Tuning needed |
| Storage | Delta Lake | Lakehouse format | Databricks integration, fast | Tied to Spark |
| Storage | Snowflake | Cloud warehouse | Zero management, separation | Cost at scale |
| Catalog | DataHub | Metadata search | Column-level lineage, GraphQL | Deployment complexity |
| Quality | Great Expectations | Data validation | Rich expectations | Slow on big data |
| Quality | dbt tests | Built-in quality | SQL-native, fast | Limited to dbt models |

**Links**: [[System-Design/Databases/Data Engineering/09 Cost Optimization]] | [[System-Design/Databases/Data Engineering/01 Fundamentals]] | [[System-Design/Databases/Data Engineering/07 Real-Time Streaming]]
**See also**: [[Apache Airflow]], [[Apache Spark]], [[Delta Lake and Apache Iceberg]]
