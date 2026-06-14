---
id: a1b2c3d4-1040-4000-8000-000000000040
title: Event-Driven Architecture
language: markdown
tags: [system-design, architecture, event-driven]
selection: null
isPinned: false
timestamp: 1781500001040
---
# Event-Driven Architecture

Event-driven architecture (EDA) uses events to trigger and communicate between decoupled services. Services publish events without knowing who consumes them.

## Core Concepts

| Concept | Description |
|---------|-------------|
| Event | A record of something that happened |
| Producer | Service that emits events |
| Consumer | Service that reacts to events |
| Event Bus/Broker | Middleware that routes events |
| Event Stream | Ordered sequence of events |

## Event Types

| Type | Description | Example |
|------|-------------|---------|
| Event Notification | "Something happened" (no payload) | `order.created` |
| Event-Carried State Transfer | Full data in the event | `order.created { id, amount, items }` |
| Command | "Do something" (directed) | `send.email` (not really an event) |

## Choreography vs Orchestration

### Choreography (Decentralized)

```
Order Service → "Order Placed" → Payment Service
                                      ↓
                               "Payment Received" → Shipping Service
                                                         ↓
                                                  "Order Shipped" → Notification
```

**Pros**: Loose coupling, no single point of failure.
**Cons**: Hard to track flow, complex debugging.

### Orchestration (Centralized)

```
                    ┌── Order Service ──┐
                    │                   │
    Order Service ──┤  Saga Coordinator ├── Payment Service
                    │                   │
                    └── Shipping ───────┘
```

**Pros**: Visible workflow, easier error handling.
**Cons**: Central coordinator is a bottleneck.

## Event Sourcing

Store state as a sequence of events instead of current state:

```
State: Account Balance = $150
Events: AccountCreated($100) → Deposited($50) → Withdrew($25) → Deposited($25)
```

- **Advantage**: Complete audit trail, temporal queries, rebuild state
- **Tradeoff**: Event store size, reconstructing current state (snapshots help)

## Saga Pattern (Distributed Transactions)

Each step publishes an event that triggers the next step. Compensation events roll back on failure.

```
Book Hotel → Car Reserved → Flight Booked
    ↓ (fail)         ↓ (compensate)
Cancel Hotel   ← Cancel Car ← Cancel Flight
```

## Event Broker Comparison

| Broker | Persistence | Ordering | Use Case |
|--------|-------------|----------|----------|
| Kafka | Disk, configurable retention | Per-partition | High-throughput event streaming |
| RabbitMQ | Memory/disk | Per-queue | Message routing, RPC |
| AWS SNS/SQS | Managed | Best-effort | Serverless, cloud-native |
| Redis Pub/Sub | None (in-memory) | No | Real-time, ephemeral |
| Pulsar | Tiered storage | Per-partition | Geo-replication, multi-tenant |

## Idempotency

Consumers must handle duplicate events gracefully:

```python
def handle_order_created(event):
    if db.exists("processed_events", event.id):
        return  # already processed
    process_order(event.data)
    db.save("processed_events", event.id)  # mark as processed
```

## When to Use EDA

**Good fit**:
- Systems with many independent services
- Real-time processing needs
- Workflows with multiple steps
- Audit/logging requirements

**Bad fit**:
- Simple CRUD apps
- Strongly consistent transactions
- Tight coupling is acceptable
- Small teams with limited operational experience

**Links**: [[Stream Processing]] | [[Event Sourcing]] | [[Message Queues]] | [[Microservices Architecture]] | [[Domain-Driven Design]]
