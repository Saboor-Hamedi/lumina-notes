---
id: 5fca2416-b9a3-402c-99fa-70189da3fbe7
title: SQLite Reference
language: markdown
tags: [system-design, databases, sqlite, relational]
selection: null
isPinned: true
timestamp: 1781317553958
---

**Links**: [[DuckDB]] | [[PostgreSQL Features]] | [[Database Engines Compared]] | [[SQL JOIN Operations]] | [[Database Indexing Deep Dive]] | [[SQL Query Optimization]]


# SQLite Reference

SQLite is a C-language library that implements a small, fast, self-contained, high-reliability, full-featured SQL database engine. It is the most widely deployed database engine in the world.

## Key Features

- **Zero-configuration**: No setup or administration needed
- **Serverless**: Runs in-process, no separate server process
- **Single-file database**: Entire DB is one file
- **Cross-platform**: Works on all major OSes
- **Self-contained**: Minimal dependencies

## Data Types (Type Affinity)

| Type | Affinity |
|------|----------|
| TEXT | String storage |
| INTEGER | 1-8 byte integer |
| REAL | Floating point |
| BLOB | Binary data |
| NUMERIC | Numeric with flexible storage |

## Limitations

- No concurrent writes (single writer)
- No user management
- Limited ALTER TABLE support
- 140TB max database size
- No stored procedures

## Use Cases

- Mobile apps (Android, iOS)
- Embedded systems
- Desktop applications
- Development/testing
- Data analysis prototypes

**See also**: [[Database Engines Compared]], [[PostgreSQL Features]], [[SQL JOIN Operations]]
