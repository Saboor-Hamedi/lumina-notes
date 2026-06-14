---
id: a1b2c3d4-1028-4000-8000-000000000028
title: Architecture Patterns
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001028
---
# Architecture Patterns

Architecture patterns define high-level structure, component organization, and communication patterns across a software system.

## Layered (N-Tier) Architecture

```
Presentation Layer    (UI, API endpoints)
    ↓
Application Layer    (use cases, DTOs)
    ↓
Domain Layer         (business logic, entities)
    ↓
Infrastructure Layer (DB, external services, mail)
```

**Pros**: Familiar, clear separation.
**Cons**: Can become monolithic, layers may leak.

## Hexagonal Architecture (Ports & Adapters)

```
                    ┌─────────┐
  [Web] ─────────→ │  Port   │ ←──── [DB]
  [CLI] ─────────→ │ (App)   │ ←──── [Message Queue]
  [Tests] ───────→ │         │ ←──── [External API]
                    └─────────┘
```

Core business logic has no dependency on external infrastructure. Ports define interfaces => adapters implement them.

## Clean Architecture (Robert C. Martin)

```
Entities        (Enterprise business rules)
    ↓
Use Cases       (Application business rules)
    ↓
Controllers     (Adapt input/output)
    ↓
Frameworks      (DB, web, UI)
```

**Dependency Rule**: Source code dependencies point inward. Nothing in inner circle knows about outer circles.

## CQRS (Command Query Responsibility Segregation)

| Path | Writes (Commands) | Reads (Queries) |
|------|-------------------|-----------------|
| Model | Domain model | Read model (denormalized) |
| Data store | Relational DB | Read-optimized (materialized views, Elasticsearch) |
| Scale | Master writes | Multiple read replicas |

Separate read and write models. Often used with event sourcing.

## Event-Driven Architecture

```
Publisher → [Event Bus / Message Broker] → Consumer
                                          → Consumer
                                          → Consumer
```

**Patterns**:
- **Pub/Sub**: Publisher doesn't know subscribers
- **Choreography**: Services react to events independently
- **Orchestration**: Central coordinator directs flow

## Comparison

| Pattern | When to Use |
|---------|-------------|
| Layered | Simple CRUD apps, monoliths |
| Hexagonal | Testable, framework-independent core |
| Clean Architecture | Complex business logic, long-lived systems |
| CQRS | Different read/write patterns, high read load |
| Event-Driven | Loose coupling, real-time processing |
| Microservices | Independent scaling/deploy of components |

## Package by Feature

```
users/
  UserController
  UserService
  UserRepository
  User
orders/
  OrderController
  OrderService
  OrderRepository
  Order
```

Co-locates all code for a feature, rather than splitting by technical layer.

**Links**: [[Domain-Driven Design]] | [[Microservices Architecture]] | [[Code Architecture Patterns]] | [[Software Design Principles]] | [[Event Sourcing]]
