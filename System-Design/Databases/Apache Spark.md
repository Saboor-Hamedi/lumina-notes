---
id: a1b2c3d4-1069-4000-8000-000000000069
title: Apache Spark
language: markdown
tags: [system-design, databases, spark, distributed-computing]
selection: null
isPinned: false
timestamp: 1781500001069
---

**Links**: [[Apache Spark Deep Dive]] | [[Apache Flink]] | [[Data Engineering]] | [[ETL and Data Pipeline Patterns]] | [[Stream Processing]] | [[Data Warehouse Modeling]]


# Apache Spark

Apache Spark is a unified analytics engine for large-scale data processing. It handles batch, streaming, SQL, ML, and graph workloads in a single framework.

## Why Spark?

| Problem | Spark Solution |
|---------|---------------|
| MapReduce is slow | In-memory computation (up to 100x faster) |
| Too many tools | Unified: batch + streaming + SQL + ML |
| Complex distributed programming | High-level APIs (DataFrame, SQL, MLlib) |
| Iterative algorithms | Cached RDDs/DataFrames |

## Architecture

```
┌─────────────────────────────────────────────────────┐
│  Driver Program                                      │
│  ┌─────────┐                                         │
│  │ SparkContext │                                     │
│  └──────┬──────┘                                     │
│         │                                             │
│  Cluster Manager (Standalone / YARN / Mesos / K8s)    │
│         │                                             │
│  ┌──────┴────────────────────────────────────────┐  │
│  │  Worker Node 1                    Worker Node 2 │  │
│  │  ┌──────────┐  ┌──────────┐      ┌──────────┐ │  │
│  │  │ Executor │  │ Executor │      │ Executor │ │  │
│  │  │ Cache +  │  │ Cache +  │      │ Cache +  │ │  │
│  │  │ Tasks    │  │ Tasks    │      │ Tasks    │ │  │
│  │  └──────────┘  └──────────┘      └──────────┘ │  │
│  └────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────┘
```

## RDD vs DataFrame vs Dataset

| Aspect | RDD | DataFrame | Dataset |
|--------|-----|-----------|---------|
| Type safety | Yes (compile-time) | No | Yes |
| Optimization | Manual | Catalyst optimizer | Catalyst optimizer |
| Language | Scala, Python, Java | All | Scala, Java |
| Performance | Slow (no optimization) | Fast (Tungsten) | Fast |
| Schema | Schema-less | Schema-inferred/defined | Schema-defined |

**Best practice**: Use DataFrames for most work; RDDs for low-level control.

## DataFrame API

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("analytics") \
    .config("spark.sql.adaptive.enabled", "true") \
    .getOrCreate()

# Read
df = spark.read.parquet("s3://data/events/")

# Transform
result = (df
    .filter(df.event_type == "purchase")
    .groupBy("user_id", "product_id")
    .agg(
        count("*").alias("purchase_count"),
        sum("amount").alias("total_spent")
    )
    .orderBy("total_spent", ascending=False)
)

# Write
result.write.mode("overwrite").parquet("s3://analytics/top_purchases/")
```

## Spark SQL

```python
df.createOrReplaceTempView("events")

result = spark.sql("""
    SELECT
        user_id,
        product_id,
        COUNT(*) as purchase_count,
        SUM(amount) as total_spent
    FROM events
    WHERE event_type = 'purchase'
    GROUP BY user_id, product_id
    ORDER BY total_spent DESC
""")
```

## Spark Streaming (Structured Streaming)

```python
# Process Kafka stream in micro-batches
stream = (spark
    .readStream
    .format("kafka")
    .option("subscribe", "events")
    .load()
    .selectExpr("CAST(value AS STRING)")
    .select(from_json("value", schema).alias("data"))
    .select("data.*")
)

# Aggregate by 1-minute windows
aggregated = (stream
    .groupBy(
        window("timestamp", "1 minute"),
        "event_type"
    )
    .count()
)

# Write to sink
query = (aggregated
    .writeStream
    .outputMode("complete")
    .format("console")
    .trigger(processingTime="1 minute")
    .start()
)

query.awaitTermination()
```

## Catalyst Optimizer

```
DataFrame code → Logical Plan → Optimization → Physical Plan → Code Generation → RDDs
                                      ↓
                              Predicate pushdown,
                              Constant folding,
                              Column pruning,
                              Join reordering
```

## Tungsten Engine

- Off-heap memory management
- Cache-aware computation
- Code generation (whole-stage)
- Columnar storage (Parquet vectorized reads)

## Spark MLlib

```python
from pyspark.ml.feature import VectorAssembler, StandardScaler
from pyspark.ml.classification import RandomForestClassifier
from pyspark.ml import Pipeline

pipeline = Pipeline(stages=[
    VectorAssembler(inputCols=["age", "income", "score"], outputCol="features"),
    StandardScaler(inputCol="features", outputCol="scaled_features"),
    RandomForestClassifier(labelCol="label", featuresCol="scaled_features")
])

model = pipeline.fit(train_df)
predictions = model.transform(test_df)
```

**Links**: [[Data Engineering]] | [[Stream Processing]] | [[SQL vs NoSQL Databases]] | [[Cloud Computing]] | [[Data Structures]]
