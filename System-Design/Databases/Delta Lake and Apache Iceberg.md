---
id: a1b2c3d4-1173-4000-8000-000000000173
title: Delta Lake and Apache Iceberg
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001173
---

**Links**: [[Data Warehouse Modeling]] | [[Apache Spark Deep Dive]] | [[Data Engineering]] | [[ETL and Data Pipeline Patterns]] | [[Apache Airflow]] | [[Snowflake and Data Warehousing]]


# Delta Lake and Apache Iceberg

Delta Lake and Apache Iceberg are open-source table formats that bring ACID transactions, schema evolution, and time travel to data lakes.

## What They Solve

```
Traditional data lake:
  s3://data/orders/dt=2026-06-13/
  ├── part-00001.parquet   (10:00:00 - written)
  ├── part-00002.parquet   (10:00:01 - written)
  └── part-00003.parquet   (10:05:00 - overwritten!)

Problems:
  - No ACID: reader sees partial writes
  - No schema enforcement
  - No versioning
  - No concurrent write safety

Lakehouse table format adds a metadata layer:
  transaction log + versioned manifests
```

## Delta Lake

Delta Lake (by Databricks) uses a transaction log stored as JSON files in a `_delta_log` directory.

```
s3://data/orders/
├── _delta_log/
│   ├── 00000000000000000000.json  (initial schema + data)
│   ├── 00000000000000000001.json  (INSERT)
│   ├── 00000000000000000002.json  (UPDATE)
│   └── 00000000000000000003.json  (DELETE)
├── part-00001-abc.parquet
├── part-00002-def.parquet
└── part-00003-ghi.parquet
```

```python
from delta import *

# Write
spark = DeltaLake.spark()
(df.write
 .format("delta")
 .mode("overwrite")
 .save("s3://data/orders"))

# Read
df = spark.read.format("delta").load("s3://data/orders")

# Time travel (version)
df = spark.read.format("delta").option("versionAsOf", 2).load("s3://data/orders")

# Time travel (timestamp)
df = spark.read.format("delta").option("timestampAsOf", "2026-06-13").load("s3://data/orders")
```

### Delta Operations

```python
# Upsert (MERGE)
from delta.tables import *

delta = DeltaTable.forPath(spark, "s3://data/orders")
delta.alias("target") \
    .merge(
        updates.alias("source"),
        "target.order_id = source.order_id"
    ) \
    .whenMatchedUpdateAll() \
    .whenNotMatchedInsertAll() \
    .execute()

# Schema evolution
spark.conf.set("spark.databricks.delta.schema.autoUpdate.enabled", "true")

# Vacuum (clean old files)
delta.vacuum(retentionHours=168)  # Keep 7 days
```

### Delta Features

| Feature | Description |
|---------|-------------|
| ACID transactions | Serializable isolation |
| Schema evolution | Add/drop columns automatically |
| Time travel | Query previous versions |
| Vacuum | Garbage collect old files |
| Z-ordering | Multi-dimensional clustering |
| Generate expressions | Pre-computed columns |
| Change Data Feed | Track row-level changes |

## Apache Iceberg

Iceberg (originally Netflix) uses a hierarchical metadata tree.

```
s3://data/orders/
├── metadata/
│   ├── v1.metadata.json        (schema, partition spec, snapshot info)
│   ├── v2.metadata.json
│   ├── snap-123456789.avro     (manifest list)
│   └── snap-987654321.avro
├── data/
│   ├── partition_key=1/
│   └── partition_key=2/
```

### Iceberg Operations

```python
# Create table
spark.sql("""
    CREATE TABLE orders (
        order_id BIGINT,
        customer_id BIGINT,
        amount DECIMAL(10,2),
        order_date DATE
    )
    USING iceberg
    PARTITIONED BY (order_date)
    LOCATION 's3://data/orders'
""")

# Time travel
spark.read
    .option("snapshot-id", 123456789)
    .table("orders")

spark.read
    .option("as-of-timestamp", "2026-06-13T10:00:00Z")
    .table("orders")

# Schema evolution
ALTER TABLE orders ADD COLUMN status STRING;
ALTER TABLE orders RENAME COLUMN amount TO total_amount;
ALTER TABLE orders ALTER COLUMN customer_id TYPE BIGINT;

# Partition evolution (changes take effect for new data)
ALTER TABLE orders ADD PARTITION FIELD status;

# Expire snapshots
spark.sql("CALL system.expire_snapshots('orders', TIMESTAMP '2026-06-06')");
```

## Comparison

| Feature | Delta Lake | Apache Iceberg |
|---------|------------|----------------|
| Originated | Databricks | Netflix/Apple |
| Format | Parquet + JSON log | Parquet + Avro metadata |
| Engine support | Spark, Flink, Presto, Trino | Spark, Flink, Trino, Hive, Presto, Dremio |
| Partition evolution | Requires rewrite | Supported natively |
| Hidden partitioning | No | Yes |
| Merge on read | Yes | Yes |
| Optimistic concurrency | Yes | Yes |
| Catalog integration | Hive Metastore, Unity | Hive, REST, DynamoDB, Nessie, JDBC |

## When to Use Which

```
Delta Lake:
  - Best if you're already in Databricks ecosystem
  - Need simple setup with Spark
  - Z-ordering for query optimization
  - Unity Catalog integration

Apache Iceberg:
  - Multi-engine environment (Spark, Flink, Trino, Dremio)
  - Need hidden partitioning (no partition column management)
  - Need partition evolution without rewriting
  - Open source, no vendor lock-in
```

## Writing to Both

```python
# Generic pattern
df.write \
    .format("iceberg") \
    .mode("append") \
    .save("s3://data/orders")

df.write \
    .format("delta") \
    .mode("append") \
    .save("s3://data/orders")
```
