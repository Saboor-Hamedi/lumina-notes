---
id: a1b2c3d4-0021-4000-8000-000000000021
title: Microservices Architecture
language: markdown
tags: [system-design, architecture, microservices]
selection: null
isPinned: false
timestamp: 1781400000021
---
# Microservices Architecture

Microservices decompose an application into independently deployable services that each own their data and domain.

## Monolith vs Microservices

| Aspect | Monolith | Microservices |
|--------|----------|---------------|
| Deployment | Single unit | Independent per service |
| Scaling | Scale entire app | Scale individual services |
| Team autonomy | Low | High |
| Complexity | Low (simple app) | High (distributed) |
| Testing | End-to-end | Per service + integration |

## Key Patterns

- **API Gateway**: Single entry point routing to services
- **Service Discovery**: Locate service instances dynamically
- **Circuit Breaker**: Fail fast when downstream is down
- **Saga**: Distributed transaction with compensating actions
- **CQRS**: Separate read and write models

## Challenges

- Network latency and failures
- Data consistency across services
- Observability (distributed tracing)
- Deployment complexity

**Links**: [[API Gateway Patterns]] | [[Code Architecture Patterns]] | [[Message Queues]] | [[REST API Design]] | [[Docker Containers]] | [[Kubernetes Basics]] | [[Service Mesh]] | [[Event-Driven Architecture]] | [[Domain-Driven Design]] | [[Saga and Distributed Transactions]] | [[Distributed Tracing]]

**See also**: [[Caching Strategies]], [[Database Sharding]], [[Monitoring and Observability]], [[CI CD Pipelines]]