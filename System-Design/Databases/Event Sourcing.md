---
id: new-014-0000-0000-0000-000000000014
title: Event Sourcing
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781600000014
---
# Event Sourcing

**Links**: [[Message Queues]] | [[Database Transactions]] | [[Microservices Architecture]] | [[Caching Strategies]] | [[Code Architecture Patterns]]

## What is Event Sourcing?

Event sourcing stores every state-changing event as an append-only log, rather than storing just the current state. The current state is derived by replaying all events.

```
Traditional:  Account( balance = 100 )
Event Sourced: Deposit(50) → Withdraw(20) → Deposit(30) → Withdraw(10)
               Replay: 0 + 50 - 20 + 30 - 10 = 50
```

## Benefits

| Benefit | Explanation |
|---------|-------------|
| **Audit trail** | Complete history of every change |
| **Debugging** | Replay events to reproduce states |
| **Temporal queries** | What was the state at any point in time? |
| **Event-driven** | Natural fit for microservices |
| **Flexible projections** | Build different read models from same events |

## Event Structure

```json
{
  "event_id": "evt_abc123",
  "event_type": "OrderPlaced",
  "aggregate_id": "order_456",
  "version": 3,
  "timestamp": "2024-01-15T10:30:00Z",
  "data": {
    "customer_id": "cust_789",
    "items": ["item_1", "item_2"],
    "total": 49.99
  },
  "metadata": {
    "user_id": "user_123",
    "ip_address": "192.168.1.1"
  }
}
```

## CQRS Pattern

Command Query Responsibility Segregation separates writes (events) from reads (projections):

```
Command Side                     Query Side
┌──────────────┐            ┌──────────────┐
│ Place Order  │ ──Event──→ │ Read Model   │
│ Cancel Order │   Store    │ (Postgres)   │
│ Update Item  │            │ (Redis Cache)│
└──────────────┘            └──────────────┘
       │                          ↑
       ↓                          │
   Event Store ──Projector───────
   (append-only)
```

## Challenges

- **Storage**: Event stores grow indefinitely (snapshot periodically)
- **Complexity**: Event versioning, schema evolution
- **Eventual consistency**: Read models lag behind writes
- **Learning curve**: Different mental model than CRUD

**Next**: [[Service Mesh]] — Manage microservice communication