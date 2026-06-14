---
id: a1b2c3d4-1155-4000-8000-000000000155
title: Apache Spark Deep Dive
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001155
---
# Apache Spark Deep Dive

Spark is a unified analytics engine for large-scale data processing. It provides batch processing, streaming, SQL, machine learning, and graph processing in a single framework.

## Architecture

```
Driver Program (SparkContext)
    │
    ├── Cluster Manager (YARN, Mesos, K8s, Standalone)
    │
    └── Executors (on worker nodes)
         ├── Cache (in-memory storage)
         ├── Task threads
         └── Block Manager

RDD → DAG Scheduler → Task Scheduler → Executors
```

## Core Abstractions

| API | Description | When to Use |
|-----|-------------|-------------|
| RDD | Low-level distributed collection | Custom operations, performance-critical |
| DataFrame | Schema-based, optimized via Catalyst | Most ETL and analytics |
| Dataset | Type-safe DataFrame (JVM only) | Type safety with Catalyst optimization |
| Spark SQL | SQL queries on DataFrames | Interactive analytics |

## RDD (Resilient Distributed Dataset)

```python
# Create from collection
rdd = spark.sparkContext.parallelize([1, 2, 3, 4, 5], numSlices=4)

# Create from file
rdd = spark.sparkContext.textFile("s3://bucket/input/*.gz")

# Transformations (lazy)
mapped = rdd.map(lambda x: x * 2)
filtered = rdd.filter(lambda x: x > 2)
flat = rdd.flatMap(lambda x: [x, x * 10])

# Actions (trigger computation)
result = mapped.collect()      # All data to driver
count = mapped.count()         # Count elements
first = mapped.first()         # First element
```

## DataFrame API

```python
from pyspark.sql import functions as F

# Read
df = spark.read.parquet("s3://data/orders/")
df = spark.read.format("delta").load("s3://data/orders/")
df = spark.read.json("s3://data/events/*.json")

# Transformations
result = (df
    .filter(F.col("amount") > 100)
    .groupBy("customer_id")
    .agg(
        F.count("*").alias("order_count"),
        F.sum("amount").alias("total_spent"),
        F.avg("amount").alias("avg_order_value")
    )
    .withColumn("tier", F.when(F.col("total_spent") > 10000, "gold").otherwise("silver"))
    .orderBy(F.col("total_spent").desc())
)

# Write
result.write.mode("overwrite").parquet("s3://data/customer_summary/")
result.write.mode("append").format("delta").save("s3://data/customer_summary_delta/")
```

## Spark SQL

```python
# Register as temp view
df.createOrReplaceTempView("orders")

# SQL queries
result = spark.sql("""
    SELECT
        customer_id,
        COUNT(*) as order_count,
        SUM(amount) as total_spent
    FROM orders
    WHERE amount > 100
    GROUP BY customer_id
    HAVING order_count > 5
    ORDER BY total_spent DESC
""")
```

## Catalyst Optimizer

```
SQL Query → DataFrame/Dataset
    │
    ▼
Unresolved Logical Plan
    │
    ▼
Analyzed Logical Plan (with catalog info)
    │
    ▼
Optimized Logical Plan (predicate pushdown, constant folding, projection pruning)
    │
    ▼
Physical Plans (cost-based optimization)
    │
    ▼
Selected Physical Plan
    │
    ▼
Code Generation (Whole-stage codegen)
    │
    ▼
RDDs (execution)
```

## Tungsten Execution Engine

Optimizations:
- **Off-heap memory management** — avoids GC overhead
- **Cache-aware computation** — leverages CPU caches
- **Whole-stage code generation** — generates JVM bytecode for tight loops
- **Vectorized processing** — processes batches of rows

## Shuffling

Shuffling moves data between executors. It's the most expensive operation.

```python
# Causes shuffle:
df.groupBy("key")               # aggregation
df.join(other, "key")            # join
df.orderBy("col")                # sorting
df.distinct()                    # deduplication
df.repartition(100)              # repartitioning

# Avoids shuffle:
df.filter(...)                   # narrow dependency
df.select(...)                   # narrow dependency
df.withColumn(...)               # narrow dependency
df.mapPartitions(...)            # per-partition processing
```

**Shuffle tuning**:

```python
# Number of shuffle partitions (default 200)
spark.conf.set("spark.sql.shuffle.partitions", 500)

# Shuffle memory fraction
spark.conf.set("spark.sql.shuffle.spill.compress", "true")
spark.conf.set("spark.shuffle.compress", "true")

# Shuffle read/write buffer size
spark.conf.set("spark.shuffle.file.buffer", "64k")
```

## Spark Streaming (Structured Streaming)

```python
# Read from Kafka
stream = (spark
    .readStream
    .format("kafka")
    .option("kafka.bootstrap.servers", "localhost:9092")
    .option("subscribe", "orders")
    .load()
    .selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")
)

# Process
processed = (stream
    .select("value")
    .writeStream
    .outputMode("append")
    .trigger(processingTime="10 seconds")
    .format("parquet")
    .option("path", "s3://data/streaming_orders/")
    .option("checkpointLocation", "s3://data/checkpoints/orders")
    .start()
)

# Windowing
windowed = (df
    .groupBy(F.window("timestamp", "1 hour", "15 minutes"), "customer_id")
    .agg(F.sum("amount").alias("hourly_spend"))
)

# Watermarking (handles late data)
windowed = (df
    .withWatermark("timestamp", "10 minutes")
    .groupBy(F.window("timestamp", "1 hour"), "customer_id")
    .agg(F.sum("amount"))
)
```

## Partitioning and Bucketing

```python
# Partition by column (directory-based)
df.write.partitionBy("year", "month").parquet("s3://data/orders/")
# s3://data/orders/year=2026/month=06/

# Bucketing (fixed number of files)
df.write.bucketBy(16, "customer_id").sortBy("order_date").saveAsTable("orders_bucketed")
```

## Tuning Checklist

```python
# Memory
spark.conf.set("spark.executor.memory", "8g")
spark.conf.set("spark.executor.memoryOverhead", "2g")
spark.conf.set("spark.memory.offHeap.enabled", "true")
spark.conf.set("spark.memory.offHeap.size", "4g")

# Parallelism
spark.conf.set("spark.executor.cores", "4")
spark.conf.set("spark.executor.instances", "20")
spark.conf.set("spark.sql.shuffle.partitions", "200")
spark.conf.set("spark.default.parallelism", "200")

# Serialization (Kryo is faster than Java)
spark.conf.set("spark.serializer", "org.apache.spark.serializer.KryoSerializer")
spark.conf.set("spark.kryo.registrationRequired", "true")

# Adaptive Query Execution (Spark 3+)
spark.conf.set("spark.sql.adaptive.enabled", "true")
spark.conf.set("spark.sql.adaptive.coalescePartitions.enabled", "true")
spark.conf.set("spark.sql.adaptive.skewJoin.enabled", "true")
```
