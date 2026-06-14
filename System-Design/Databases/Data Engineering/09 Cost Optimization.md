---
tags: [data-engineering, cost-optimization, snowflake]
---

# 09 — Cost Optimization

## Snowflake

| Strategy | Savings | Implementation |
|----------|---------|----------------|
| Auto-suspend | 30-60% | Set warehouse auto-suspend to 1-5 min |
| Multi-cluster auto-scales | 20-40% | Set max clusters = 2-3 |
| Materialized queries | 20-50% | Cache expensive aggregations |
| Clustering keys | 10-30% | Cluster on frequently filtered columns |
| Data compression | 15-30% | Use appropriate data types |
| Resource monitors | Prevents overrun | Monthly credit caps with alerts |

```sql
-- Warehouse credit usage
SELECT warehouse_name, SUM(credits_used) AS total_credits
FROM snowflake.account_usage.warehouse_metering_history
WHERE start_time >= DATEADD('month', -1, CURRENT_DATE)
GROUP BY 1 ORDER BY 2 DESC;

-- Expensive queries
SELECT query_id, query_text, warehouse_size, credits_used_cloud_services,
    execution_time / 1000 AS execution_seconds
FROM snowflake.account_usage.query_history
WHERE start_time >= DATEADD('day', -7, CURRENT_DATE)
ORDER BY credits_used_cloud_services DESC;
```

## General Cost Strategies

| Area | Strategy | Impact |
|------|----------|--------|
| Storage | Compress (Parquet/Zstd) | 50-75% reduction |
| Storage | Tiered storage (S3 → Glacier) | 60-80% reduction |
| Compute | Right-size clusters | 30-50% reduction |
| Compute | Spot/preemptible instances | 60-90% reduction |
| Compute | Serverless (auto-scaling) | Pay per query |
| Data | Partition pruning | 50-90% scan reduction |
| Data | Incremental processing | 70-95% reduction |
| Pipeline | Consolidate small files | Faster reads, less overhead |
| Pipeline | Cache intermediate results | Avoid recomputation |

## Technology Stack Decision Flow

```
Data Volume?
├── GBs → DuckDB + dbt → BI (Metabase/Superset)
├── TBs →
│   ├── Batch → Airflow + dbt + Snowflake/BQ
│   ├── Streaming → Kafka + Flink + Iceberg
│   └── Mixed → Spark Structured Streaming + Delta Lake
└── PBs → Spark/Databricks → ML Feature Store
```

**Links**: [[System-Design/Databases/Data Engineering/10 Quick Reference]] | [[System-Design/Databases/Data Engineering/01 Fundamentals]] | [[System-Design/Databases/Data Engineering/02 Architecture & Medallion]]
**See also**: [[Cloud Computing]], [[Data Warehouse Modeling]]
