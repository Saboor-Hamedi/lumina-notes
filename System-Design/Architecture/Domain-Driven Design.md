---
id: a1b2c3d4-1027-4000-8000-000000000027
title: Domain-Driven Design
language: markdown
tags: [system-design, architecture, ddd]
selection: null
isPinned: false
timestamp: 1781500001027
---
# Domain-Driven Design

Domain-Driven Design (DDD) focuses modeling software around the business domain, using a shared language between developers and domain experts.

## Building Blocks

| Concept | Description |
|---------|-------------|
| Entity | Object with a unique identity (e.g., User, Order) |
| Value Object | Immutable object defined by its attributes (e.g., Money, Address) |
| Aggregate | Cluster of entities/value objects treated as a unit |
| Aggregate Root | Entry point for accessing an aggregate |
| Repository | Persistence abstraction for aggregates |
| Domain Service | Stateless operations that don't fit entities |
| Domain Event | Something significant that happened in the domain |
| Factory | Creates complex aggregates/entities |

## Ubiquitous Language

The domain model's terminology must be shared between devs and domain experts. Every term in code matches the business vocabulary exactly.

```
Business: "A customer can place an order with multiple line items"
Code:     Customer.placeOrder(items: List<LineItem>) -> Order
```

## Bounded Context

A bounded context is a boundary within which a particular domain model applies. Different contexts may use the same term differently.

```
Sales Context: "Product" has price and inventory
Shipping Context: "Product" has weight and dimensions
```

## Strategic Patterns

| Pattern | Purpose |
|---------|---------|
| Core Domain | Competitive advantage (invest most effort) |
| Supporting Subdomain | Needed but not core (buy or outsource) |
| Generic Subdomain | Standard functionality (use off-the-shelf) |
| Context Map | Relationships between bounded contexts |

## Context Relationships

| Relationship | Description |
|-------------|-------------|
| Partnership | Two teams coordinate changes |
| Shared Kernel | Share a subset of the model |
| Customer-Supplier | Upstream provides, downstream consumes |
| Conformist | Downstream conforms to upstream model |
| Anti-Corruption Layer | Translates between contexts |
| Open-Host Service | Published protocol for integration |
| Separated Ways | No integration between contexts |

## Tactical Design Example

```python
# Value Object
@dataclass(frozen=True)
class Money:
    amount: Decimal
    currency: str

# Entity
class Order:
    def __init__(self, order_id: str):
        self.id = order_id
        self.items: list[LineItem] = []
        self.status = OrderStatus.PENDING

    def add_item(self, product: Product, quantity: int):
        self.items.append(LineItem(product, quantity))

    def total(self) -> Money:
        return Money(
            sum(item.subtotal() for item in self.items),
            "USD"
        )

# Aggregate Root: Order manages its LineItems
# Repository: OrderRepository handles persistence
```

**Links**: [[Microservices Architecture]] | [[Architecture Patterns]] | [[Code Architecture Patterns]] | [[Event Sourcing]] | [[Object-Oriented Programming]]
