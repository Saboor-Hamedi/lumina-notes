---
tags: [data-engineering, architecture, medallion, lakehouse]
---

# 02 — Architecture & Medallion

## Modern Data Pipeline Architecture

```mermaid
flowchart LR
    subgraph "Sources"
        APIS[REST APIs] & DB[(OLTP DBs)] & LOGS[App Logs] & SAAS[SaaS Tools] & IOT[IoT Devices]
    end
    subgraph "Ingestion"
        KAFKA[Kafka / Kinesis] & AIRBYTE[Airbyte / Fivetran] & CDC[CDC: Debezium] & STREAM[Kafka Streams]
    end
    subgraph "Storage"
        DL[Data Lake: S3/ADLS/GCS]
        DW[Data Warehouse: Snowflake/BQ/Redshift]
        LH[Lakehouse: Iceberg/Delta/Parquet]
    end
    subgraph "Transformation"
        DBT[dbt] & SPARK[Spark] & FLINK[Flink]
    end
    subgraph "Orchestration"
        AF[Airflow] & DG[Dagster] & PF[Prefect]
    end
    subgraph "Serving"
        BI[Tableau/Looker] & ML[ML Models] & REV[Reverse ETL]
    end
    APIS & DB & LOGS & SAAS & IOT --> KAFKA & AIRBYTE & CDC
    AIRBYTE & CDC --> DL & DW
    KAFKA --> DL & STREAM --> FLINK
    DL --> SPARK --> DW
    DL & DW --> DBT --> DW & LH
    SPARK & DBT & FLINK --> AF & DG & PF
    DW & LH --> BI & ML & REV
```

## Data Lake vs Warehouse vs Lakehouse

| Capability | Data Lake | Data Warehouse | Lakehouse |
|-----------|-----------|----------------|-----------|
| Data format | Raw (any format) | Structured, SQL | Open formats (Parquet, Iceberg) |
| Schema | Schema-on-read | Schema-on-write | Schema-on-write + evolution |
| Storage cost | Low (~$23/TB S3) | Higher (~$300/TB BQ) | Low (S3 + metadata) |
| ACID | Limited | Full | Full (Iceberg/Delta) |
| ML support | Native | Limited | Native |
| BI support | Limited | Excellent | Good |

## Medallion Architecture

```mermaid
flowchart LR
    subgraph "Bronze"
        B1[Raw Ingest] & B2[Full history] & B3[Immutable]
    end
    subgraph "Silver"
        S1[Cleaned] & S2[Deduplicated] & S3[Validated]
    end
    subgraph "Gold"
        G1[Aggregated] & G2[Business Entities] & G3[Ready for Consumption]
    end
    B1 & B2 & B3 -->|dbt/Spark| S1 & S2 & S3
    S1 & S2 & S3 --> G1 & G2 & G3
```

| Layer | Characteristics |
|-------|----------------|
| **Bronze** | Raw data as-is, append-only, immutable, preserves schema evolution |
| **Silver** | Deduplicated, type-cast, standardized, quality checks applied |
| **Gold** | Business aggregates, star schema, feature engineering, BI-ready |

**Links**: [[System-Design/Databases/Data Engineering/01 Fundamentals]] | [[System-Design/Databases/Data Engineering/03 dbt]] | [[System-Design/Databases/Data Engineering/06 Reverse ETL & Data Mesh]]
**See also**: [[Delta Lake and Apache Iceberg]], [[Data Warehouse Modeling]]
