---
id: da8107ed-9237-4e63-89c3-730c84c04d38
title: PostgreSQL Features
language: markdown
tags: [system-design, databases, postgresql, relational]
selection: null
isPinned: false
timestamp: 1781195166785
---

**Links**: [[PostgreSQL Extensions]] | [[PostgreSQL Performance Tuning]] | [[Database Engines Compared]] | [[SQL Query Optimization]] | [[Database Indexing Deep Dive]] | [[SQL JOIN Operations]]


# PostgreSQL Features

PostgreSQL is the world's most advanced open-source relational database. Its feature set rivals commercial databases.

## Advanced Data Types

| Type | Description |
|------|-------------|
| JSONB | Binary JSON with indexing |
| Array | Multi-dimensional arrays |
| hstore | Key-value store within a column |
| UUID | Universally unique identifiers |
| Geometric | Points, lines, polygons |
| Network | INET, CIDR for IP addresses |
| Range | Date, numeric ranges with overlap operators |
| Full-text | tsvector/tsquery for search |

## Indexing

| Index Type | Use Case |
|------------|----------|
| B-tree | Default, equality + range |
| Hash | Equality lookups |
| GiST | Full-text, geometry |
| GIN | JSONB, arrays, full-text |
| BRIN | Large sorted tables |
| SP-GiST | Partitioned search trees |

## Extensions

- `pgvector` — Vector similarity search
- `PostGIS` — Geospatial data
- `pg_stat_statements` — Query performance
- `uuid-ossp` — UUID generation
- `pgcrypto` — Cryptographic functions

**See also**: [[Database Engines Compared]], [[SQL JOIN Operations]], [[SQL Query Optimization]], [[Data Normalization Rules]], [[DB Relationship Patterns]], [[SQLite Reference]]
