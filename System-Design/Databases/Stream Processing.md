---
id: a1b2c3d4-1012-4000-8000-000000000012
title: Stream Processing
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001012
---
# Stream Processing

Stream processing handles unbounded data in real time, enabling low-latency analytics, event-driven architectures, and data pipelines.

## Batch vs Stream

| Aspect | Batch | Stream |
|--------|-------|--------|
| Data | Bounded (finite) | Unbounded (infinite) |
| Latency | Minutes to hours | Milliseconds to seconds |
| Processing | Periodic jobs | Continuous |
| Storage | Data lake, warehouse | Message queue, log |
| Example | Daily ETL | Fraud detection |

## Core Concepts

- **Event**: A record of something that happened
- **Stream**: An ordered, replayable sequence of events
- **Producer**: Writes events to a stream
- **Consumer**: Reads and processes events
- **Topic/Channel**: Named stream category
- **Partition**: Sharded segment of a topic for parallelism

## Event Processing Patterns

| Pattern | Description |
|---------|-------------|
| Event Sourcing | Store state as sequence of events |
| CQRS | Separate read and write paths |
| Stream Processing | Transform/filter/aggregate on the fly |
| Complex Event Processing | Detect patterns across events |
| Kappa Architecture | One pipeline for both real-time and batch |

## Stream Processing Frameworks

| Framework | Model | Best For |
|-----------|-------|----------|
| Apache Kafka | Distributed log | High-throughput event streaming |
| Apache Flink | True streaming (not micro-batch) | Stateful computations |
| Kafka Streams | Embedded library | Lightweight, JVM-based |
| Apache Spark Streaming | Micro-batch | Large-scale analytics |
| Apache Pulsar | Multi-tenant log | Geo-replication, low latency |

## Kafka Concepts

```
 Producer → [Topic: Partition 0] ← Consumer Group A
 Producer → [Topic: Partition 1] ← Consumer Group A
 Producer → [Topic: Partition 2] ← Consumer Group B
```

- **Offset**: Position of a message within a partition
- **Consumer Group**: Distributes partitions across members
- **Broker**: A Kafka server node
- **Replication Factor**: Number of copies for fault tolerance

## Exactly-Once Semantics

| Guarantee | Meaning |
|-----------|---------|
| At-most-once | Message delivered 0 or 1 times (durable, possible loss) |
| At-least-once | Message delivered 1+ times (no loss, possible duplicates) |
| Exactly-once | Message delivered exactly once (idempotent + transactional) |

## Use Cases

- Fraud detection (sub-second decisions)
- Real-time analytics (dashboards, monitoring)
- Log aggregation (centralized logging)
- Change Data Capture (CDC from databases)
- Metrics and monitoring (time-series data)
- Event-driven microservices

**Links**: [[Message Queues]] | [[Event Sourcing]] | [[Microservices Architecture]] | [[Database Connection Pooling]] | [[Monitoring and Observability]]
