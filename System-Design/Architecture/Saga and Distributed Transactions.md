---
id: a1b2c3d4-1178-4000-8000-000000000178
title: Saga and Distributed Transactions
language: markdown
tags: [system-design, architecture, saga, distributed-transactions]
selection: null
isPinned: false
timestamp: 1781500001178
---
# Saga and Distributed Transactions

Sagas manage data consistency across microservices without distributed transactions (XA). A saga is a sequence of local transactions where each step publishes an event that triggers the next step.

## The Problem

```
Monolith:
  BEGIN TRANSACTION
    UPDATE orders SET status = 'confirmed' WHERE id = 123
    UPDATE inventory SET quantity = quantity - 1 WHERE product_id = 456
    UPDATE payment SET status = 'captured' WHERE order_id = 123
  COMMIT

Microservices (distributed):
  Orders Service     ──► Order confirmed
  Inventory Service  ──► Stock decreased
  Payment Service    ──► Payment captured

  No single transaction can span all three services!
  Need: Saga pattern
```

## Saga Types

### Choreography (Event-based)

```
Order Service: Create order → emits "order_created"
Inventory Service: listens → decrease stock → emits "stock_decreased"
Payment Service: listens → capture payment → emits "payment_captured"
Order Service: listens → update order status → "order_confirmed"

On failure:
  Payment Service: fails → emits "payment_failed"
  Order Service: listens → cancel order
  Inventory Service: listens → restock inventory (compensating action)
```

```python
class OrderSaga:
    def handle(self, event):
        if event.type == "order_created":
            # Try to reserve inventory
            self.emit("reserve_inventory", {"order_id": event.data["order_id"]})

        elif event.type == "inventory_reserved":
            # Try to capture payment
            self.emit("capture_payment", {"order_id": event.data["order_id"]})

        elif event.type == "payment_captured":
            # Confirm order
            self.emit("confirm_order", {"order_id": event.data["order_id"]})

        # Compensating actions (rollback)
        elif event.type == "inventory_reservation_failed":
            self.emit("cancel_order", {"order_id": event.data["order_id"]})

        elif event.type == "payment_failed":
            self.emit("release_inventory", {"order_id": event.data["order_id"]})
```

### Orchestration (Command-based)

```
Orchestrator Service coordinates the saga:
  1. Saga starts → command "reserve_inventory" to Inventory Service
  2. Inventory responds → command "capture_payment" to Payment Service
  3. Payment responds → command "confirm_order" to Order Service

  Orchestrator tracks state, handles retries, and executes compensating transactions
  on failure. Centralized coordination but more control.
```

```python
from enum import Enum

class SagaState(Enum):
    PENDING = "pending"
    INVENTORY_RESERVED = "inventory_reserved"
    PAYMENT_CAPTURED = "payment_captured"
    COMPLETED = "completed"
    FAILED = "failed"

class OrderSagaOrchestrator:
    def __init__(self):
        self.sagas = {}  # order_id → state

    async def start(self, order_id):
        saga = SagaInstance(order_id, SagaState.PENDING)
        self.sagas[order_id] = saga

        try:
            await self.inventory_service.reserve(order_id)
            saga.state = SagaState.INVENTORY_RESERVED

            await self.payment_service.capture(order_id)
            saga.state = SagaState.PAYMENT_CAPTURED

            await self.order_service.confirm(order_id)
            saga.state = SagaState.COMPLETED

        except Exception as e:
            await self.compensate(saga)
            raise SagaFailed(order_id, e)

    async def compensate(self, saga):
        if saga.state == SagaState.INVENTORY_RESERVED:
            await self.inventory_service.release(saga.order_id)

        saga.state = SagaState.FAILED
```

## Compensating Transactions

| Step | Transaction | Compensating Transaction |
|------|-------------|-------------------------|
| Reserve inventory | Decrease available count | Increase available count |
| Capture payment | Debit customer | Refund customer |
| Book flight | Reserve seat | Cancel reservation |
| Send email | Send confirmation | Send apology email |
| Create record | INSERT row | DELETE row |

## Idempotency

```python
# All saga steps must be idempotent (retry-safe)

def reserve_inventory(order_id, product_id, quantity):
    """Idempotent — detects duplicate requests."""
    reservation = db.query_one(
        "SELECT * FROM reservations WHERE order_id = %s", [order_id]
    )
    if reservation:
        # Already reserved — idempotent
        return reservation

    return db.execute(
        "UPDATE inventory SET reserved = reserved + %s "
        "WHERE product_id = %s AND available - reserved >= %s "
        "RETURNING *",
        [quantity, product_id, quantity]
    )
```

## Saga State Persistence

```python
class SagaStore:
    def save(self, saga):
        self.db.execute(
            "INSERT INTO sagas (saga_id, saga_type, state, step, payload) "
            "VALUES (%s, %s, %s, %s, %s) "
            "ON CONFLICT (saga_id) DO UPDATE SET "
            "state = EXCLUDED.state, step = EXCLUDED.step",
            [saga.id, saga.type, saga.state.value, saga.current_step,
             json.dumps(saga.payload)]
        )

    def recover_pending(self):
        """Recover sagas that were in-flight when the service crashed."""
        rows = self.db.query(
            "SELECT * FROM sagas WHERE state IN ('pending', 'inventory_reserved', 'payment_captured')"
        )
        return [self._restore(row) for row in rows]
```

## Timeout Handling

```python
async def run_with_timeout(saga_id, saga_coro, timeout_seconds=30):
    try:
        await asyncio.wait_for(saga_coro, timeout=timeout_seconds)
    except asyncio.TimeoutError:
        # Saga timed out — execute compensations for completed steps
        saga = saga_store.get(saga_id)
        await orchestrator.compensate(saga)
        raise SagaTimeout(saga_id)
```

## Comparison

| Aspect | Choreography | Orchestration |
|--------|-------------|---------------|
| Coordination | Decentralized | Centralized |
| Complexity | Lower (simple flows) | Higher (but more control) |
| Coupling | Event-based (loose) | Command-based (tighter) |
| Failure handling | Distributed logic | Single orchestrator |
| Visibility | Harder to trace | Easy (orchestrator state) |
| Cyclic dependencies | Possible | Avoided |
| Scale | Large number of services | Fewer services |

**Links**: [[Architecture Patterns]] | [[Blockchain Fundamentals]] | [[CAP Theorem and PACELC]] | [[CDN Architecture]] | [[Code Architecture Patterns]] | [[Computer Architecture and Organization]] | [[Computer Networking]] | [[Concurrency Models]] | [[DNS Deep Dive]] | [[Domain-Driven Design]] | [[Event-Driven Architecture]] | [[Istio Service Mesh]] | [[Memory Management]] | [[Microservices Architecture]] | [[Operating Systems]] | [[Raft Consensus Algorithm]] | [[Serverless Computing]] | [[Service Mesh]] | [[System Design Fundamentals]]
