---
id: 4d29dadb-4490-42aa-b1ca-02f27614be82
title: Code Architecture Patterns
language: markdown
tags: [system-design, architecture, code-architecture, design-patterns]
selection: null
isPinned: true
timestamp: 1780926621322
---
# Code Architecture Patterns

Software architecture patterns provide proven solutions to common design problems.

## Layered Architecture

```
Presentation Layer  (UI / API)
    ↓
Business Logic Layer  (services)
    ↓
Data Access Layer  (repositories)
    ↓
Database Layer
```

## MVC (Model-View-Controller)

| Component | Responsibility |
|-----------|----------------|
| Model | Data and business logic |
| View | User interface / presentation |
| Controller | Handles input, updates model/view |

## Microservices

Independent services that communicate via APIs. Each service owns its data and domain.

## Event-Driven Architecture

Components communicate via events through a message broker (Kafka, RabbitMQ).

## Repository Pattern

Abstracts data access behind an interface, making the data layer swappable.

```python
class UserRepository:
    def get_by_id(self, id): ...
    def save(self, user): ...

class PostgresUserRepository(UserRepository):
    def get_by_id(self, id):
        return db.query("SELECT * FROM users WHERE id = %s", id)
```

**See also**: [[Software Design Principles]], [[REST API Design]], [[Programming Language Paradigms]], [[Unit Testing Guide]]

**Links**: [[Architecture Patterns]] | [[Blockchain Fundamentals]] | [[CAP Theorem and PACELC]] | [[CDN Architecture]] | [[Computer Architecture and Organization]] | [[Computer Networking]] | [[Concurrency Models]] | [[DNS Deep Dive]] | [[Domain-Driven Design]] | [[Event-Driven Architecture]] | [[Istio Service Mesh]] | [[Memory Management]] | [[Microservices Architecture]] | [[Operating Systems]] | [[Raft Consensus Algorithm]] | [[Saga and Distributed Transactions]] | [[Serverless Computing]] | [[Service Mesh]] | [[System Design Fundamentals]]
