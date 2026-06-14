---
id: moc-db-0000-0000-0000-000000000019
title: Databases — Map of Content
language: markdown
tags: [moc, index, databases]
isPinned: true
timestamp: 1781700000019
---

# 🗄️ Databases — Map of Content

**Parent**: [[System-Design/_MOC|System Design]]

## Categories

| Category | Databases |
|----------|-----------|
| **Relational** | [[PostgreSQL Features]], [[PostgreSQL Performance Tuning]], [[PostgreSQL Extensions]], [[SQLite Reference]], [[SQL vs NoSQL Databases]] |
| **Document** | [[MongoDB]], [[MongoDB Deep Dive]] |
| **Key-Value** | [[Redis Deep Dive]] |
| **Wide-Column** | [[Cassandra]], [[Apache Cassandra Deep Dive]] |
| **Search** | [[Elasticsearch Deep Dive]], [[Search Engines]] |
| **Graph** | [[Neo4j and Graph Databases]], [[Graph Databases]] |
| **Time Series** | [[Time Series Databases]] |
| **Streaming** | [[Apache Kafka Deep Dive]], [[Apache Flink]], [[Stream Processing]] |
| **OLAP** | [[ClickHouse]], [[DuckDB]], [[Snowflake and Data Warehousing]], [[Apache Spark]] |
| **Workflow** | [[Apache Airflow]] |
| **NewSQL** | [[CRDTs]], [[Delta Lake and Apache Iceberg]] |

## Core Concepts

| Concept | Related Notes |
|---------|---------------|
| **Indexing** | [[Database Indexing Deep Dive]], [[Database Indexing Strategies]], [[LSM-Tree Storage Engines]] |
| **Transactions** | [[Database Transactions]], [[Database Transaction Isolation Levels]] |
| **Scaling** | [[Database Sharding]], [[Database Replication Strategies]], [[Consistent Hashing]] |
| **Caching** | [[Caching Strategies]] |
| **Modeling** | [[Data Warehouse Modeling]], [[Data Normalization Rules]], [[DB Relationship Patterns]] |
| **Querying** | [[SQL JOIN Operations]], [[SQL Query Optimization]] |
| **Architecture** | [[Event Sourcing]], [[Event Sourcing Deep Dive]], [[Message Queues]], [[Database Engines Compared]] |

## Key Comparisons

```
                     SQL                     NoSQL
              ┌─────────────────────────────────────┐
  Schema      │  Fixed               Dynamic         │
  ACID        │  Strong              Varies          │
  Scale       │  Vertical            Horizontal      │
  Consistency │  Strong              Eventual        │
  Queries     │  Complex JOINs       Simple lookups  │
              └─────────────────────────────────────┘
```

## Cross-Domain Links

- [[System-Design/Databases/Caching Strategies]] → [[Web-Dev/HTTP Caching]], [[System-Design/Architecture/CDN Architecture]]
- [[System-Design/Databases/Apache Kafka Deep Dive]] → [[System-Design/Architecture/Event-Driven Architecture]]
- [[System-Design/Databases/PostgreSQL Features]] → [[System-Design/Databases/PostgreSQL Performance Tuning]], [[System-Design/Databases/PostgreSQL Extensions]]
- [[System-Design/Databases/Database Security]] → [[Security/Database Security]], [[Security/Vault and Secret Management]]
