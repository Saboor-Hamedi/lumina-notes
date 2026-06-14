---
id: a1b2c3d4-1037-4000-8000-000000000037
title: System Design Interview
language: markdown
tags: [software-engineering]
selection: null
isPinned: false
timestamp: 1781500001037
---
# System Design Interview

System design interviews assess your ability to design large-scale distributed systems. The focus is on tradeoffs, reasoning, and breadth.

## Interview Framework (4-Step)

### 1. Requirements Gathering (5 min)

| Type | Questions |
|------|-----------|
| Functional | What features? Create/read/update/delete what? |
| Non-functional | Latency, consistency, durability, availability? |
| Scale | DAU, reads/sec, writes/sec, storage? |

**Be explicit**: "Let's design a URL shortener that handles 100M URLs, 10K writes/sec, 100K reads/sec with P99 < 100ms."

### 2. High-Level Design (10 min)

```
Client
  ↓
Load Balancer
  ↓
API Servers
  ↓
Database
```

Draw boxes and arrows. Name major components. Show data flow for the key write and read paths.

### 3. Deep Dive (15 min)

Choose 1-2 areas to explore in depth:

| Area | Questions |
|------|-----------|
| Database | Schema, indexing, sharding strategy |
| Caching | What to cache, eviction policy, CDN |
| Consistency | Strong vs eventual, replication |
| Performance | Connection pooling, async, batch |

### 4. Wrap-Up (5 min)

| Topic | Example |
|-------|---------|
| Bottlenecks | DB write throughput, cache hit rate |
| Tradeoffs | Consistency vs availability |
| Scaling | Future growth, geo-distribution |
| Failure modes | What breaks and how we handle it |

## Common Questions

| Problem | Key Challenge |
|---------|---------------|
| URL Shortener | Key generation, 301 vs 302 |
| Chat System | Real-time delivery, ordering |
| News Feed | Fanout on write vs fanout on read |
| Rate Limiter | Distributed counters, sliding window |
| Design Pastebin | Text storage, expiration, dedup |
| Design Uber | Location tracking, matching algorithm |
| Design Twitter | Timeline generation, push vs pull |
| Design YouTube | Video upload, transcoding pipeline |
| Design Dropbox | File sync, conflict resolution |
| Design Web Crawler | URL dedup, politeness, freshness |

## Estimation (Back-of-Envelope)

| Metric | Rule of Thumb |
|--------|---------------|
| DAU → QPS | 100M DAU, 10 req/day → ~12K QPS peak |
| Storage | Each operation produces X bytes |
| Bandwidth | QPS × response size |
| Caching | 80-20 rule (cache 20% hottest data) |
| Server capacity | 1000-10000 req/sec per core (approx) |

## Key Tradeoffs

| Tradeoff | Considerations |
|----------|---------------|
| SQL vs NoSQL | Schema, joins, scale, consistency |
| Monolith vs Microservices | Complexity, deploy speed, team size |
| Push vs Pull | Real-time vs backpressure |
| Sync vs Async | Latency vs decoupling |
| Vertical vs Horizontal | Simplicity vs elasticity |

## Evaluation Criteria

| Criterion | What They Look For |
|-----------|-------------------|
| Requirements | Asked clarifying questions, defined scope |
| Structure | Clear framework, organized thinking |
| Tradeoffs | Mentioned pros/cons of decisions |
| Depth | Deep dive into 1-2 components |
| Communication | Explained reasoning clearly |

**Links**: [[System Design Fundamentals]] | [[Microservices Architecture]] | [[Database Sharding]] | [[Caching Strategies]] | [[REST API Design]]
