---
id: a1b2c3d4-1177-4000-8000-000000000177
title: Event Sourcing Deep Dive
language: markdown
tags: [system-design, databases, event-sourcing, architecture]
selection: null
isPinned: false
timestamp: 1781500001177
---

**Links**: [[Event Sourcing]] | [[CRDTs]] | [[Message Queues]] | [[Database Transactions]] | [[Stream Processing]] | [[Apache Kafka Deep Dive]]


# Event Sourcing Deep Dive

Event sourcing persists state changes as an append-only log of events. Current state is derived by replaying events.

## Core Concept

```
Traditional storage:
  orders table: { id: 123, status: "shipped", total: 99.99 }

Event sourcing:
  events table:
    { type: "order_placed",    order_id: 123, data: { total: 99.99 }, version: 1 }
    { type: "order_paid",      order_id: 123, data: { amount: 99.99 }, version: 2 }
    { type: "order_shipped",   order_id: 123, data: { carrier: "UPS" }, version: 3 }
    { type: "order_delivered", order_id: 123, data: { signed_by: "Alice" }, version: 4 }

  Current state = replay events 1-4
```

## Event Structure

```json
{
    "id": "evt_abc123",
    "type": "order_placed",
    "aggregate_id": "order_123",
    "aggregate_type": "order",
    "version": 1,
    "data": {
        "customer_id": "cust_456",
        "items": [
            { "product_id": "prod_789", "quantity": 2, "price": 49.99 }
        ],
        "total": 99.98,
        "shipping_address": "123 Main St"
    },
    "metadata": {
        "causation_id": "cmd_xyz789",
        "correlation_id": "corr_def456",
        "timestamp": "2026-06-13T10:00:00Z",
        "user_id": "user_789"
    }
}
```

## Aggregate (Command Side)

```python
from dataclasses import dataclass
from typing import List
import uuid

@dataclass
class Event:
    event_id: str
    aggregate_id: str
    event_type: str
    data: dict
    version: int

class OrderAggregate:
    def __init__(self, order_id):
        self.id = order_id
        self.status = "pending"
        self.items = []
        self.total = 0
        self.version = 0
        self.changes = []

    def place_order(self, customer_id, items):
        if self.status != "pending":
            raise ValueError("Order already placed")
        event = self._create_event("order_placed", {
            "customer_id": customer_id,
            "items": items,
            "total": sum(item["price"] * item["quantity"] for item in items),
        })
        self._apply(event)

    def mark_paid(self, payment_id, amount):
        if self.status not in ("pending", "placed"):
            raise ValueError("Order cannot be paid")
        event = self._create_event("order_paid", {
            "payment_id": payment_id,
            "amount": amount,
        })
        self._apply(event)

    def _create_event(self, event_type, data):
        return Event(
            event_id=str(uuid.uuid4()),
            aggregate_id=self.id,
            event_type=event_type,
            data=data,
            version=self.version + 1,
        )

    def _apply(self, event):
        if event.event_type == "order_placed":
            self.status = "placed"
            self.items = event.data["items"]
            self.total = event.data["total"]
        elif event.event_type == "order_paid":
            self.status = "paid"
        elif event.event_type == "order_shipped":
            self.status = "shipped"

        self.version = event.version
        self.changes.append(event)

    @classmethod
    def load_from_history(cls, order_id, events):
        aggregate = cls(order_id)
        for event in events:
            aggregate._apply(event)
        return aggregate
```

## Event Store

```python
class EventStore:
    def __init__(self, db):
        self.db = db

    def save_events(self, aggregate_id, events, expected_version):
        """Optimistic concurrency — atomically append events."""
        with self.db.transaction():
            current = self._get_version(aggregate_id)
            if current != expected_version:
                raise ConcurrencyError(
                    f"Expected version {expected_version}, got {current}"
                )
            for event in events:
                self.db.execute(
                    "INSERT INTO events (event_id, aggregate_id, event_type, data, version) "
                    "VALUES (%s, %s, %s, %s, %s)",
                    [event.event_id, event.aggregate_id,
                     event.event_type, json.dumps(event.data), event.version]
                )

    def get_events(self, aggregate_id):
        rows = self.db.query(
            "SELECT * FROM events WHERE aggregate_id = %s ORDER BY version",
            [aggregate_id]
        )
        return [Event(**row) for row in rows]
```

## Projections (Read Side)

```python
class OrderProjection:
    """Denormalized read model — updated by consuming events."""
    def __init__(self, db):
        self.db = db

    def handle_event(self, event):
        if event.event_type == "order_placed":
            self.db.execute(
                "INSERT INTO order_read_model "
                "(order_id, customer_id, total, status, created_at) "
                "VALUES (%s, %s, %s, 'placed', %s)",
                [event.aggregate_id, event.data["customer_id"],
                 event.data["total"], event.metadata["timestamp"]]
            )
        elif event.event_type == "order_paid":
            self.db.execute(
                "UPDATE order_read_model SET status = 'paid' WHERE order_id = %s",
                [event.aggregate_id]
            )
```

## Snapshotting

```python
# Replaying all events becomes expensive for long-lived aggregates
# Solution: periodic snapshots

class SnapshotStore:
    def save_snapshot(self, aggregate_id, state, version):
        self.db.execute(
            "UPSERT INTO snapshots (aggregate_id, state, version) "
            "VALUES (%s, %s, %s)",
            [aggregate_id, json.dumps(state), version]
        )

    def load_snapshot(self, aggregate_id):
        row = self.db.query_one(
            "SELECT state, version FROM snapshots WHERE aggregate_id = %s",
            [aggregate_id]
        )
        if row:
            return json.loads(row["state"]), row["version"]
        return None, 0
```

## Benefits and Trade-offs

| Pro | Con |
|-----|-----|
| Complete audit trail | Event schema evolution complexity |
| Temporal queries (state at any time) | Event store is append-only (no deletes) |
| Debugging (replay events) | Eventual consistency of read models |
| Strong consistency within aggregate | Distributed transactions across aggregates |
| Natural for CQRS | Learning curve |
| Rebuild projections from scratch | Snapshotting needed for long-lived aggregates |
