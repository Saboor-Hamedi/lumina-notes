---
id: moc-sd-0000-0000-0000-000000000003
title: System Design — Map of Content
language: markdown
tags: [moc, index, system-design]
isPinned: true
timestamp: 1781700000003
---

# 🏗️ System Design — Map of Content

```mermaid
graph TD
  SD["System Design"] --> ARCH["Architecture"]
  SD --> DB["Databases"]
  SD --> ALGO["Algorithms"]
  SD --> DS["Data Structures"]

  ARCH --> SDF["System Design Fundamentals"]
  ARCH --> AP["Architecture Patterns"]
  ARCH --> MA["Microservices Architecture"]
  ARCH --> EDA["Event-Driven Architecture"]
  ARCH --> DDD["Domain-Driven Design"]
  ARCH --> CAP["CAP Theorem & PACELC"]
  ARCH --> CDN["CDN Architecture"]
  ARCH --> DNS["DNS Deep Dive"]
  ARCH --> RAFT["Raft Consensus Algorithm"]
  ARCH --> SAGA["Saga & Distributed Transactions"]
  ARCH --> SM["Service Mesh"]
  ARCH --> ISM["Istio Service Mesh"]
  ARCH --> SL["Serverless Computing"]
  ARCH --> CN["Computer Networking"]
  ARCH --> CA["Computer Architecture"]
  ARCH --> OS["Operating Systems"]
  ARCH --> MM["Memory Management"]
  ARCH --> CM["Concurrency Models"]
  ARCH --> BC["Blockchain Fundamentals"]
  ARCH --> CAP2["Code Architecture Patterns"]

  DB --> SQL["SQL vs NoSQL Databases"]
  DB --> DI["Database Indexing"]
  DB --> DSHARD["Database Sharding"]
  DB --> DREP["Database Replication"]
  DB --> DTX["Database Transactions"]
  DB --> DTI["Transaction Isolation Levels"]
  DB --> CACH["Caching Strategies"]
  DB --> CH["Consistent Hashing"]
  DB --> ES["Event Sourcing"]
  DB --> MQ["Message Queues"]
  DB --> SP["Stream Processing"]
  DB --> PG["PostgreSQL Features"]
  DB --> PGP["PostgreSQL Performance Tuning"]
  DB --> MONGO["MongoDB"]
  DB --> CASS["Cassandra"]
  DB --> REDIS["Redis Deep Dive"]
  DB --> ES2["Elasticsearch Deep Dive"]
  DB --> KAFKA["Apache Kafka Deep Dive"]
  DB --> SPARK["Apache Spark"]
  DB --> FLINK["Apache Flink"]
  DB --> AIRF["Apache Airflow"]
  DB --> CH2["ClickHouse"]
  DB --> DUCK["DuckDB"]
  DB --> SQLITE["SQLite Reference"]
  DB --> NEO4J["Neo4j & Graph Databases"]
  DB --> TSDB["Time Series Databases"]
  DB --> SE["Search Engines"]
  DB --> RL["Rate Limiting"]
  DB --> DE["Data Engineering"]
  DB --> ETL["ETL & Data Pipeline Patterns"]
  DB --> DW["Data Warehouse Modeling"]
  DB --> NORM["Data Normalization Rules"]
  DB --> SER["Data Serialization"]
  DB --> DEC["Database Engines Compared"]
  DB --> SNOW["Snowflake & Data Warehousing"]
  DB --> DL["Delta Lake & Apache Iceberg"]
  DB --> LSM["LSM-Tree Storage Engines"]
  DB --> CRDT["CRDTs"]
  DB --> UUID["UUID vs Snowflake vs ULID"]
  DB --> DBP["Database Connection Pooling"]
  DB --> DBM["Database Migration Tools"]
  DB --> DBACK["Database Backup Strategies"]
  DB --> DSEC["Database Security"]
  DB --> DTRIG["Database Triggers"]
  DB --> DV["Database Views"]
  DB --> REL["DB Relationship Patterns"]
  DB --> JOIN["SQL JOIN Operations"]
  DB --> QO["SQL Query Optimization"]

  ALGO --> BIGO["Big O Notation"]
  ALGO --> AP2["Algorithm Paradigms"]
  ALGO --> CC["Computational Complexity"]
  ALGO --> GA["Graph Algorithms"]
  ALGO --> NM["Numerical Methods"]

  DS --> DSO["Data Structures Overview"]
  DS --> PDS["Probabilistic Data Structures"]
```

## Sub-Areas

| Area | Notes | Description |
|------|-------|-------------|
| [[System-Design/Architecture/_MOC\|Architecture]] | 20 | System design patterns, paradigms |
| [[System-Design/Databases/_MOC\|Databases]] | 55 | SQL, NoSQL, caching, streaming |
| [[System-Design/Algorithms/_MOC\|Algorithms]] | 5 | Complexity, paradigms |
| [[System-Design/Data-Structures/_MOC\|Data Structures]] | 3 | Classic & probabilistic |

## Cross-Domain Links

- [[System-Design/Databases/Caching Strategies]] → [[Web-Dev/HTTP Caching]], [[System-Design/Architecture/CDN Architecture]]
- [[System-Design/Databases/Database Sharding]] → [[DevOps/Infrastructure/Cloud Computing]]
- [[System-Design/Architecture/Microservices Architecture]] → [[DevOps/Containers/Docker Containers]], [[DevOps/Containers/Kubernetes Basics]], [[API Gateway Patterns]]
- [[System-Design/Databases/Message Queues]] → [[System-Design/Databases/Apache Kafka Deep Dive]]
- [[System-Design/Architecture/CAP Theorem and PACELC]] → [[System-Design/Databases/Cassandra]], [[System-Design/Databases/MongoDB Deep Dive]]
