---
id: db-024-0000-0000-0000-000000000042
title: PostgreSQL Extensions
language: markdown
tags: [system-design, databases, postgresql, extensions]
selection: null
isPinned: false
timestamp: 1781800000020
---
# PostgreSQL Extensions

**Links**: [[PostgreSQL Features]] | [[JSON in PostgreSQL]] | [[Full-Text Search]] | [[Vector Databases for RAG]] | [[Database Indexing Deep Dive]]

## What are Extensions?

Extensions add functionality to PostgreSQL — new data types, index methods, functions, and operators.

## Installing Extensions

```sql
-- Enable extension
CREATE EXTENSION IF NOT EXISTS extension_name;

-- List installed
SELECT * FROM pg_extension;

-- View available
SELECT * FROM pg_available_extensions;
```

## Essential Extensions

| Extension | Purpose |
|-----------|---------|
| **pgvector** | Vector similarity search (RAG) |
| **PostGIS** | Geospatial data and queries |
| **pg_stat_statements** | Query performance monitoring |
| **uuid-ossp** | UUID generation |
| **pgcrypto** | Cryptographic functions |
| **hstore** | Key-value store |
| **ltree** | Hierarchical data (tree paths) |
| **pg_trgm** | Trigram text search |
| **bloom** | Bloom filter index |
| **pg_partman** | Automated partitioning |
| **pg_bigm** | Bigram full-text search |
| **pg_repack** | Rebuild tables without locks |
| **hypopg** | Hypothetical indexes |

## pg_stat_statements

```sql
CREATE EXTENSION pg_stat_statements;

-- Top 10 queries by total time
SELECT query, total_time, calls, rows
FROM pg_stat_statements
ORDER BY total_time DESC
LIMIT 10;
```

## uuid-ossp

```sql
CREATE EXTENSION "uuid-ossp";

SELECT uuid_generate_v4();    -- Random UUID
SELECT uuid_generate_v1();    -- Time-based UUID
SELECT uuid_nil();            -- Nil UUID
```

## ltree (Hierarchies)

```sql
CREATE EXTENSION ltree;

CREATE TABLE categories (
    id SERIAL PRIMARY KEY,
    path LTREE
);

INSERT INTO categories (path) VALUES
('Root'),
('Root.Electronics'),
('Root.Electronics.Laptops');

SELECT * FROM categories
WHERE path @> 'Root.Electronics';
```

**Next**: [[Full-Text Search]] — PostgreSQL text search