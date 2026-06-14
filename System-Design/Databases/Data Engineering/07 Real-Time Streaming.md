---
tags: [data-engineering, streaming, kafka, flink, spark-streaming]
---

# 07 — Real-Time Streaming

## Kafka Streams Example

```java
StreamsBuilder builder = new StreamsBuilder();
KStream<String, Order> orders = builder.stream("orders",
    Consumed.with(Serdes.String(), orderSerde));

KTable<String, Customer> customers = builder.table("customers",
    Consumed.with(Serdes.String(), customerSerde));

KStream<String, EnrichedOrder> enriched = orders.join(customers,
    (order, customer) -> new EnrichedOrder(order, customer));

KGroupedStream<String, EnrichedOrder> byRegion = enriched
    .groupBy((key, order) -> order.getRegion());

KTable<Windowed<String>, Long> regionCounts = byRegion
    .windowedBy(TimeWindows.ofSizeWithNoGrace(Duration.ofMinutes(5)))
    .count();

regionCounts.toStream().to("region-stats");
```

## Flink vs Spark Streaming

| Aspect | Apache Flink | Spark Streaming |
|--------|-------------|-----------------|
| Processing model | True streaming (event-by-event) | Micro-batch |
| Latency | Sub-second | Seconds |
| Event time | Native (watermarks) | Supported |
| State management | Built-in (RocksDB) | Checkpointing |
| Exactly-once | Yes | Yes |
| SQL support | Flink SQL (evolving) | Spark SQL (mature) |
| CEP | Native (Flink CEP) | External libraries |
| Backpressure | Automatic | Adaptive |
| Best for | Low-latency, complex event processing | High-throughput, batch+stream |

## PySpark Structured Streaming

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import window, col

spark = SparkSession.builder.appName("StreamingAnalytics").getOrCreate()

orders = spark.readStream.format("kafka") \
    .option("kafka.bootstrap.servers", "localhost:9092") \
    .option("subscribe", "orders") \
    .load() \
    .selectExpr("CAST(value AS STRING) as json") \
    .select(from_json(col("json"), order_schema).alias("data")) \
    .select("data.*")

stats = orders.withWatermark("event_time", "10 minutes") \
    .groupBy(col("region"), window(col("event_time"), "5 minutes")) \
    .agg(sum("amount").alias("revenue"), count("*").alias("order_count"))

query = stats.writeStream.format("console").outputMode("append").start()
query.awaitTermination()
```

**Links**: [[System-Design/Databases/Data Engineering/08 Schema Evolution]] | [[System-Design/Databases/Data Engineering/04 Orchestration]] | [[System-Design/Databases/Data Engineering/01 Fundamentals]]
**See also**: [[Stream Processing]], [[Apache Kafka]], [[Apache Spark]]
