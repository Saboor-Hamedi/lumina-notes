---
id: a1b2c3d4-1009-4000-8000-000000000009
title: SQL vs NoSQL Databases
language: markdown
tags: [system-design, databases, sql, nosql]
selection: null
isPinned: false
timestamp: 1781500001009
---

**Links**: [[Database Engines Compared]] | [[MongoDB]] | [[Cassandra]] | [[PostgreSQL Features]] | [[Graph Databases]] | [[Time Series Databases]]


# SQL vs NoSQL Databases

The choice between SQL and NoSQL depends on data structure, consistency needs, scale, and query patterns.

## Comparison

| Aspect | SQL | NoSQL |
|--------|-----|-------|
| Schema | Fixed, predefined | Flexible, dynamic |
| ACID | Full ACID compliance | Base (eventual consistency) |
| Scaling | Vertical primarily | Horizontal (sharding) |
| Query | Structured (SQL) | API-based or query language |
| Joins | Native support | Denormalized or app-level joins |
| Maturity | Decades of tooling | Younger ecosystem |

## When to Choose SQL

- Structured data with clear relationships
- Complex queries, joins, aggregations
- Strong consistency required (banking, ERP)
- ACID transactions needed
- Reporting and analytics

## When to Choose NoSQL

- Rapidly evolving schema
- Massive scale (web-scale apps)
- Simple key-value lookups
- High-velocity writes (IoT, logs)
- Denormalized document storage

## NoSQL Types

| Type | Example | Best For |
|------|---------|----------|
| Document | MongoDB, Couchbase | JSON-like records, catalogs |
| Key-Value | Redis, DynamoDB | Caching, sessions, queues |
| Column-Family | Cassandra, HBase | Time series, analytics |
| Graph | Neo4j, ArangoDB | Social networks, recommendations |

## Polyglot Persistence

Modern architectures often mix databases:

```
User-facing app → PostgreSQL (transactions)
   ↓
Search → Elasticsearch
Cache → Redis
Analytics → ClickHouse
```

## Migration Paths

| From | To | Strategy |
|------|----|----------|
| SQL → NoSQL | De-normalize schema, handle joins in app |
| NoSQL → SQL | Define schema, migrate data with ETL |
| Monolith → Polyglot | Extract bounded contexts one at a time |

**Links**: [[Database Engines Compared]] | [[Database Sharding]] | [[Database Indexing Deep Dive]] | [[Data Normalization Rules]] | [[Graph Databases]]
