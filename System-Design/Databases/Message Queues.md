---
id: a1b2c3d4-0011-4000-8000-000000000011
title: Message Queues
language: markdown
tags: [system-design, databases, message-queues, async]
selection: null
isPinned: false
timestamp: 1781400000011
---

**Links**: [[Apache Kafka Deep Dive]] | [[Stream Processing]] | [[Event Sourcing]] | [[Database Transactions]] | [[Caching Strategies]] | [[Event Sourcing Deep Dive]]


# Message Queues

Message queues enable asynchronous communication between services by buffering and delivering messages.

## Queue vs Pub/Sub

| Pattern | Behavior | Examples |
|---------|----------|---------|
| Queue | One consumer per message | RabbitMQ, SQS |
| Pub/Sub | Every subscriber gets each message | Kafka, Redis Pub/Sub |

## Kafka Concepts

| Term | Meaning |
|------|---------|
| Topic | Named message stream |
| Partition | Sharded log within a topic |
| Producer | Publishes messages to topics |
| Consumer | Reads messages from topics |
| Broker | Kafka server node |
| Offset | Position in a partition log |

## Use Cases

- Decoupling microservices
- Event sourcing and CQRS
- Stream processing (real-time analytics)
- Async task offloading (email, notifications)
- Log aggregation

**Links**: [[Apache Kafka Deep Dive]] | [[Event-Driven Architecture]] | [[Event Sourcing]] | [[Stream Processing]] | [[Microservices Architecture]] | [[Concurrency Models]] | [[Code Architecture Patterns]] | [[CI CD Pipelines]]

**See also**: [[Saga and Distributed Transactions]], [[Database Transactions]], [[Apache Flink]]