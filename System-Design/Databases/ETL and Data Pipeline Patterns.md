---
id: a1b2c3d4-1156-4000-8000-000000000156
title: ETL and Data Pipeline Patterns
language: markdown
tags: [system-design, databases, etl, data-pipeline]
selection: null
isPinned: false
timestamp: 1781500001156
---

**Links**: [[Data Engineering]] | [[Apache Airflow]] | [[Apache Spark]] | [[Stream Processing]] | [[Data Warehouse Modeling]] | [[Delta Lake and Apache Iceberg]]


# ETL and Data Pipeline Patterns

ETL (Extract, Transform, Load) pipelines move and transform data from source systems to target systems for analytics, reporting, and machine learning.

## ETL vs ELT

| Aspect | ETL | ELT |
|--------|-----|-----|
| Transform location | Staging area before load | Target system after load |
| Target system | Data warehouse | Data lake / lakehouse |
| Processing power | ETL server | Target database |
| Data volume | Lower (transformed first) | Higher (raw data loaded) |
| Schema | Schema-on-write | Schema-on-read |
| Flexibility | Lower | Higher (raw data available) |

## CDC (Change Data Capture)

CDC captures incremental changes from source databases:

```sql
-- Debezium + Kafka pattern
-- Source: PostgreSQL WAL
-- Capture: INSERT, UPDATE, DELETE
-- Output: Kafka topic with before/after images

{
    "op": "c",            -- c=create, u=update, d=delete, r=snapshot
    "before": null,
    "after": {
        "id": 123,
        "name": "Alice",
        "email": "alice@example.com"
    },
    "source": {
        "db": "mydb",
        "table": "users",
        "lsn": 12345678
    },
    "ts_ms": 1718000000000
}
```

## Batch vs Streaming

| Characteristic | Batch | Streaming |
|----------------|-------|-----------|
| Latency | Hours to days | Seconds to minutes |
| Data volume | Very large | Continuous |
| Complexity | Lower | Higher |
| Exactly-once | Easier | Harder |
| Cost | Lower | Higher |
| When to use | Daily reports, backfill | Real-time dashboards, alerts |

## Idempotency

Pipelines should produce the same result when re-run:

```python
def load_incremental(spark, source_table, target_path, watermark_col, last_run):
    """Idempotent incremental load using watermark."""
    df = (spark.read
          .format("jdbc")
          .option("dbtable", f"(SELECT * FROM {source_table} "
                             f"WHERE {watermark_col} > '{last_run}') t")
          .load())

    # Deduplicate (handle re-delivery)
    df = df.dropDuplicates(["event_id", "offset"])

    df.write.mode("append").parquet(target_path)
```

## Slowly Changing Dimensions (SCD)

| Type | Strategy | Example |
|------|----------|---------|
| Type 0 | Retain original | Date dimension |
| Type 1 | Overwrite | Customer email (no history) |
| Type 2 | Add new row with version | Customer address (keep history) |
| Type 3 | Add new attribute | Customer previous address |
| Type 4 | Separate history table | Audit log |

```python
# SCD Type 2 implementation
# When customer address changes:
# - Set current record's end_date = now, active_flag = false
# - Insert new record with start_date = now, active_flag = true

def apply_scd2(customer_df, existing_df, business_key, tracking_cols):
    # Find changed records
    changed = (customer_df.alias("new")
               .join(existing_df.alias("old"), business_key)
               .filter("old.active_flag = true")
               .filter(" OR ".join(f"new.{c} != old.{c}" for c in tracking_cols))
               .select("old.*"))

    # Expire old records
    expired = changed.withColumn("end_date", F.current_timestamp()) \
                     .withColumn("active_flag", F.lit(False))

    # Insert new version
    inserted = (customer_df
                .withColumn("start_date", F.current_timestamp())
                .withColumn("end_date", F.lit(None))
                .withColumn("active_flag", F.lit(True)))

    return expired.union(inserted)
```

## Pipeline Orchestration

```yaml
# Dag definition (Airflow DAG)
dag_id: daily_etl
schedule: 0 6 * * *   # Daily at 6 AM
default_args:
  retries: 3
  retry_delay: 5min

tasks:
  - id: extract_orders
    type: SparkJob
    script: extract_orders.py
    retries: 2

  - id: load_orders
    type: SparkJob
    depends_on: extract_orders
    script: load_orders.py

  - id: refresh_aggregates
    type: SparkJob
    depends_on: load_orders
    script: daily_aggregates.py

  - id: data_quality_checks
    type: PythonOperator
    depends_on: [extract_orders, load_orders, refresh_aggregates]
    script: quality_checks.py

  - id: notify
    type: SlackNotifier
    depends_on: data_quality_checks
    message: "Daily ETL complete: {status}"
```

## Common Patterns

### Full Refresh

```python
def full_refresh(target_table, source_query, connection):
    """Replace entire table."""
    connection.execute(f"CREATE OR REPLACE TABLE {target_table} AS {source_query}")
```

### Incremental Append

```python
def incremental_append(target_table, source_df, partition_col):
    """Append new data only (for immutable events)."""
    source_df.write.mode("append").insertInto(target_table)
```

### Merge (Upsert)

```python
def upsert(spark, target_table, source_df, merge_keys):
    """Merge source into target (insert or update)."""
    source_df.createOrReplaceTempView("source")
    merge_condition = " AND ".join(f"target.{k} = source.{k}" for k in merge_keys)

    spark.sql(f"""
        MERGE INTO {target_table} AS target
        USING source ON {merge_condition}
        WHEN MATCHED THEN UPDATE SET *
        WHEN NOT MATCHED THEN INSERT *
    """)
```

### Snapshot

```python
def daily_snapshot(spark, source_table, date_col, snapshot_date):
    """Take full snapshot for each partition period."""
    df = spark.table(source_table)
    (df
     .withColumn("snapshot_date", F.lit(snapshot_date))
     .write.mode("overwrite")
     .option("replaceWhere", f"snapshot_date = '{snapshot_date}'")
     .partitionBy("snapshot_date")
     .format("delta")
     .save(f"path/to/{source_table}_snapshots"))
```

## Data Quality Checks

```python
def run_quality_checks(df, table_name):
    checks = []

    # Null check
    nulls = df.filter(F.col("id").isNull()).count()
    checks.append(("null_ids", nulls == 0, f"Found {nulls} null IDs"))

    # Uniqueness
    total = df.count()
    unique = df.select("id").distinct().count()
    checks.append(("unique_ids", total == unique, f"{total - unique} duplicates"))

    # Row count threshold
    checks.append(("row_count", total > 0, "Empty dataset"))

    # Referential integrity
    orphaned = df.filter(~F.col("customer_id").isin(valid_customer_ids)).count()
    checks.append(("orphaned_customers", orphaned == 0, f"{orphaned} orphaned rows"))

    # Fail pipeline on critical check failure
    for name, passed, message in checks:
        if not passed:
            raise ValueError(f"Quality check failed: {name}: {message}")

    return checks
```
