---
id: a1b2c3d4-1154-4000-8000-000000000154
title: Apache Kafka Deep Dive
language: markdown
tags: [system-design, databases, kafka, streaming]
selection: null
isPinned: false
timestamp: 1781500001154
---
---
id: a1b2c3d4-1154-4000-8000-000000000154
title: Apache Kafka Deep Dive
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001154
---

# Apache Kafka Deep Dive

**Links**: [[Message Queues]] | [[Event-Driven Architecture]] | [[Event Sourcing Deep Dive]] | [[Stream Processing]] | [[Database Replication Strategies]] | [[Microservices Architecture]]

**See also**: [[Apache Flink]], [[Apache Spark Deep Dive]], [[ClickHouse]], [[Monitoring and Observability]], [[Logging Best Practices]]

Kafka is a distributed event streaming platform. It provides a publish-subscribe model with durable storage, fault tolerance, and horizontal scalability.

## Architecture

```
Producer ──► Topic (Partition 0) ──► Consumer
Producer ──► Topic (Partition 1) ──► Consumer Group
Producer ──► Topic (Partition 2) ──► Consumer

           ZooKeeper / KRaft (metadata store)
```

## Core Concepts

| Concept | Description |
|---------|-------------|
| Topic | Category/feed name for messages |
| Partition | Ordered, immutable sequence of records |
| Offset | Unique ID of a record within a partition |
| Broker | Server in the Kafka cluster |
| Producer | Publishes records to topics |
| Consumer | Subscribes to topics and processes records |
| Consumer Group | Group of consumers that share work |
| Replication | Copies of partitions across brokers |
| Leader | The broker responsible for a partition |
| Follower | Replicates leader's data |

## Topics and Partitions

```bash
# Create topic with 6 partitions, replication factor 3
kafka-topics.sh --create \
    --topic orders \
    --partitions 6 \
    --replication-factor 3 \
    --bootstrap-server localhost:9092

# Describe topic
kafka-topics.sh --describe --topic orders --bootstrap-server localhost:9092

# Topic: orders  PartitionCount: 6  ReplicationFactor: 3
#   Topic: orders  Partition: 0  Leader: 1  Replicas: 1,2,3  Isr: 1,2,3
#   Topic: orders  Partition: 1  Leader: 2  Replicas: 2,3,1  Isr: 2,3,1
```

**Partition count guidelines**: 1 partition = 1 consumer max in a group. More partitions = more parallelism but more file handles and rebalance overhead.

## Producing Messages

```java
// Java producer
Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
props.put("acks", "all");           // Wait for all replicas to ack
props.put("compression.type", "snappy");
props.put("batch.size", 65536);     // 64KB batch
props.put("linger.ms", 10);         // Wait up to 10ms for batching

Producer<String, String> producer = new KafkaProducer<>(props);

for (int i = 0; i < 100; i++) {
    producer.send(new ProducerRecord<>("orders", Integer.toString(i), "order_" + i));
}
producer.close();
```

```python
# Python producer
from kafka import KafkaProducer
import json

producer = KafkaProducer(
    bootstrap_servers="localhost:9092",
    value_serializer=lambda v: json.dumps(v).encode(),
    acks="all",
    compression_type="snappy"
)

for i in range(100):
    producer.send("orders", {"order_id": i, "amount": 99.99})
producer.flush()
```

## Consuming Messages

```python
from kafka import KafkaConsumer

consumer = KafkaConsumer(
    "orders",
    bootstrap_servers="localhost:9092",
    group_id="order-processor",
    auto_offset_reset="earliest",  # Start from beginning if no committed offset
    enable_auto_commit=False,      # Manual commit for exactly-once semantics
)

for message in consumer:
    print(f"Partition: {message.partition}, Offset: {message.offset}, "
          f"Key: {message.key}, Value: {message.value}")

    # Process message...

    consumer.commit()  # Commit offset after successful processing
```

## Consumer Groups and Rebalancing

```
Group: order-processor

3 consumers, 6 partitions:
  Consumer 1: partitions 0, 1
  Consumer 2: partitions 2, 3
  Consumer 3: partitions 4, 5

Consumer 2 crashes → rebalance → new assignment:
  Consumer 1: partitions 0, 1, 2
  Consumer 3: partitions 3, 4, 5
```

**Rebalance triggers**: Consumer joins/leaves group, partition count changes, topic created/deleted.

**Cooperative rebalancing (Kafka 2.4+)**: Only revokes a subset of partitions at a time, reducing the "stop-the-world" effect.

## Delivery Semantics

| Semantics | Description | Configuration |
|-----------|-------------|---------------|
| At-most-once | Message may be lost but never redelivered | `acks=0`, auto-commit before processing |
| At-least-once | Message never lost but may be redelivered | `acks=all`, commit after processing |
| Exactly-once | Message delivered exactly once | `enable.idempotence=true`, transactional API |

```java
// Exactly-once producer
props.put("enable.idempotence", "true");
props.put("transactional.id", "order-service-1");

producer.initTransactions();
producer.beginTransaction();
producer.send(new ProducerRecord<>("orders", "key", "value"));
producer.send(new ProducerRecord<>("payments", "key", "value"));
producer.commitTransaction();
```

## Retention and Compaction

```bash
# Time-based retention (delete after 7 days)
--config retention.ms=604800000

# Size-based retention (keep last 10GB)
--config retention.bytes=10737418240

# Log compaction (keep latest value per key)
--config cleanup.policy=compact

# Hybrid: compact + delete
--config cleanup.policy=compact,delete
--config delete.retention.ms=86400000  # Delete tombstones after 1 day
```

**Log compaction**: Useful for database changelog topics where you only need the latest state per key.

## Kafka Connect

```json
// Source connector (stream database changes)
{
    "name": "mysql-connector",
    "config": {
        "connector.class": "io.confluent.connect.jdbc.JdbcSourceConnector",
        "connection.url": "jdbc:mysql://localhost:3306/mydb",
        "mode": "incrementing",
        "incrementing.column.name": "id",
        "topic.prefix": "mysql-",
    }
}

// Sink connector (write to Elasticsearch)
{
    "name": "elasticsearch-sink",
    "config": {
        "connector.class": "io.confluent.connect.elasticsearch.ElasticsearchSinkConnector",
        "connection.url": "http://localhost:9200",
        "topics": "orders",
        "key.ignore": "true",
    }
}
```

## Kafka Streams

```java
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> orders = builder.stream("orders");

KTable<String, Long> counts = orders
    .groupBy((key, value) -> extractCategory(value))
    .count();

counts.toStream().to("order-counts", Produced.with(Serdes.String(), Serdes.Long()));
```

## Performance Characteristics

| Aspect | Typical |
|--------|---------|
| Throughput per broker | ~100-500 MB/s |
| Latency | ~2-10ms (end-to-end, no processing) |
| Max partition per broker | ~4000 (practical limit) |
| Message size limit | 1MB (default), configurable higher |
| Retention | Configurable (time or size) |

## Monitoring

```bash
# Key metrics to monitor
kafka-consumer-groups.sh --describe --group my-group --bootstrap-server localhost:9092
# LAG (difference between latest offset and consumer offset) — critical

# JMX metrics
kafka.topic:type=Partition,name=UnderReplicatedPartitions
kafka.server:type=BrokerTopicMetrics,name=BytesInPerSec
kafka.consumer:type=consumer-fetch-manager-metrics,name=records-lag-max
```
