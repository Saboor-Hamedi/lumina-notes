---
id: a1b2c3d4-1001-4000-8000-000000000001
title: System Design Fundamentals
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001001
---
# System Design Fundamentals

System design is the process of defining architecture, components, modules, and data flow to meet functional and non-functional requirements.

## Key Principles

| Principle | Description |
|-----------|-------------|
| Separation of Concerns | Divide into distinct layers/services |
| Single Responsibility | Each component does one thing |
| Loose Coupling | Minimize dependencies between components |
| High Cohesion | Related logic stays together |
| Redundancy | Duplicate critical components for fault tolerance |
| Statelessness | Prefer stateless designs for horizontal scaling |

## CAP Theorem

A distributed system can only guarantee two of three properties:

| Property | Meaning |
|----------|---------|
| Consistency | Every read returns the most recent write |
| Availability | Every request gets a non-error response |
| Partition Tolerance | System continues despite network failures |

**Practical choices**: CP (consistency over availability) for banking; AP (availability over consistency) for social media; CA is not realistic in distributed systems.

## Consistency Models

| Model | Guarantee |
|-------|-----------|
| Strong | Read always sees latest write |
| Eventual | Reads eventually converge |
| Causal | Related operations ordered, unrelated can diverge |
| Read-Your-Writes | Client sees its own writes |

## Load Balancing

- **Round Robin**: Distributes evenly, doesn't consider load
- **Least Connections**: Sends to least busy server
- **IP Hash**: Consistent routing by client IP (good for sticky sessions)
- **Weighted**: Servers with higher capacity get more traffic

## Caching Layers

```
Client → CDN → Load Balancer → App Server → In-Memory Cache → Database
```

## Database Scaling

| Strategy | Description |
|----------|-------------|
| Vertical | Increase server resources (RAM, CPU) |
| Read Replicas | Copy data to read-only nodes |
| Sharding | Split data across databases by key |
| Partitioning | Split tables within a database |

**Links**: [[Microservices Architecture]] | [[Database Sharding]] | [[Caching Strategies]] | [[REST API Design]] | [[Message Queues]]
