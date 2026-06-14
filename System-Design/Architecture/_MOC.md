---
id: moc-arch-0000-0000-0000-000000000018
title: Architecture — Map of Content
language: markdown
tags: [moc, index, architecture]
isPinned: true
timestamp: 1781700000018
---

# 🏛️ Architecture — Map of Content

**Parent**: [[System-Design/_MOC|System Design]]

System architecture defines how components interact, scale, and fail. This folder covers fundamental concepts (CAP theorem, consensus algorithms), architectural patterns (microservices, event-driven, DDD), infrastructure primitives (DNS, CDN, service mesh), and distributed systems theory (Raft, Saga, blockchain). These notes prepare you to design, evaluate, and communicate system architectures.

```mermaid
mindmap
  root((Architecture))
    Fundamentals
      System_Design_Fundamentals
      CAP_Theorem_and_PACELC
      Computer_Networking
      Operating_Systems
      Memory_Management
      Concurrency_Models
      Computer_Architecture_and_Organization
    Patterns
      Architecture_Patterns
      Microservices_Architecture
      Event-Driven_Architecture
      Domain-Driven_Design
      Code_Architecture_Patterns
      Serverless_Computing
    Infrastructure
      CDN_Architecture
      DNS_Deep_Dive
      Service_Mesh
      Istio_Service_Mesh
    Distributed Systems
      Raft_Consensus_Algorithm
      Saga_and_Distributed_Transactions
      Blockchain_Fundamentals
```

## Topics

| Category | Notes |
|----------|-------|
| **Fundamentals** | [[System Design Fundamentals]], [[CAP Theorem and PACELC]], [[Computer Networking]], [[Operating Systems]], [[Memory Management]], [[Concurrency Models]], [[Computer Architecture and Organization]] |
| **Patterns** | [[Architecture Patterns]], [[Microservices Architecture]], [[Event-Driven Architecture]], [[Domain-Driven Design]], [[Code Architecture Patterns]], [[Serverless Computing]] |
| **Infrastructure** | [[CDN Architecture]], [[DNS Deep Dive]], [[Service Mesh]], [[Istio Service Mesh]] |
| **Distributed** | [[Raft Consensus Algorithm]], [[Saga and Distributed Transactions]], [[Blockchain Fundamentals]] |

## Cross-Domain Links

- [[System-Design/Architecture/Microservices Architecture]] → [[DevOps/Containers/Docker Containers]], [[DevOps/Containers/Kubernetes Basics]]
- [[System-Design/Architecture/CAP Theorem and PACELC]] → [[System-Design/Databases/Cassandra]], [[System-Design/Databases/MongoDB Deep Dive]]
- [[System-Design/Architecture/Event-Driven Architecture]] → [[System-Design/Databases/Message Queues]], [[System-Design/Databases/Kafka Deep Dive]]
- [[System-Design/Architecture/CDN Architecture]] → [[Web-Dev/HTTP Caching]], [[System-Design/Databases/Caching Strategies]]
