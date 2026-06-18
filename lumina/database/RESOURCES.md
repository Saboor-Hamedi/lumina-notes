---
id: RESOURCES
title: RESOURCES
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781790154033.3884
---
# PostgreSQL Resources

## Knowledge (Vault — already captured)

- [[System-Design/Databases/PostgreSQL Features]] — MVCC, WAL, TOAST, vacuum, indexing types, JSONB
  Use for: foundation reference, architecture overview
- [[System-Design/Databases/PostgreSQL Performance Tuning]] — memory, I/O, query planning, connection pooling, vacuum tuning
  Use for: config reference, metrics, monitoring queries
- [[System-Design/Databases/PostgreSQL Extensions]] — pgvector, PostGIS, pg_stat_statements, pg_partman
  Use for: extension survey, selection guide
- [[System-Design/Databases/Database Indexing Deep Dive]] — B-tree, GiST, GIN, BRIN internals
  Use for: understanding index tradeoffs
- [[System-Design/Databases/SQL Query Optimization]] — EXPLAIN ANALYZE, scan types, join strategies
  Use for: query tuning reference
- [[System-Design/Databases/Database Transaction Isolation Levels]] — MVCC details, SSI, snapshot isolation
  Use for: concurrency understanding
- [[System-Design/Databases/Database Sharding]] — horizontal scaling, Citus, partitioning
  Use for: advanced scaling patterns
- [[System-Design/Databases/Database Replication Strategies]] — streaming replication, logical replication, failover
  Use for: HA architecture

## Knowledge (External)

- [PostgreSQL Official Documentation](https://www.postgresql.org/docs/current/) — the canonical source
  Use for: authoritative answers on any feature
- [Use the Index, Luke!](https://use-the-index-luke.com/) — comprehensive guide to SQL indexing
  Use for: index strategy deep dives
- [pganalyze Blog](https://pganalyze.com/blog) — practical PostgreSQL performance articles
  Use for: real-world tuning patterns
- [PostgreSQL Weekly](https://pglinker.com/) — curated news, articles, and tool announcements
  Use for: staying current with the ecosystem

## Wisdom (Communities)

- [PostgreSQL subreddit](https://reddit.com/r/PostgreSQL) — active community for Q&A and discussion
  Use for: troubleshooting, best practices discussion
- [PostgreSQL Discord / IRC](https://www.postgresql.org/community/) — real-time help from contributors
  Use for: quick questions, debugging live issues
- [Stack Overflow (postgresql tag)](https://stackoverflow.com/questions/tagged/postgresql) — vast archive of solved problems
  Use for: specific "how do I X" queries
