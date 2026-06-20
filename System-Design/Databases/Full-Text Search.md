---
id: db-026-0000-0000-0000-000000000050
title: Full-Text Search
language: markdown
tags:
  - system-design
  - databases
  - search
selection: null
isPinned: false
customIcon: null
timestamp: 1781958071318
---

# Full-Text Search

Full-text search enables searching natural language documents by linguistic matching — handling stemming, ranking, stop words, and fuzzy matching — rather than simple substring matching with `LIKE`.

## Why Not LIKE?

```sql
-- Slow, no ranking, no stemming
SELECT * FROM articles WHERE body LIKE '%running%';
```

`LIKE` scans every row, ignores word boundaries, and can't rank results. Full-text search addresses all of these.

## PostgreSQL Full-Text Search

### tsvector and tsquery

```sql
-- Convert text to searchable vector
SELECT to_tsvector('english', 'The cats are running quickly');
-- 'cat':2 'quick':4 'run':3

-- Match with query
SELECT to_tsvector('english', 'The cats are running quickly')
       @@ to_tsquery('english', 'run & cat');
-- true (stemming matched 'running' → 'run', 'cats' → 'cat')
```

### Creating an Index

```sql
CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    title TEXT,
    body TEXT,
    search_vector tsvector
);

-- Populate the vector column
UPDATE articles SET search_vector =
    to_tsvector('english', coalesce(title, '') || ' ' || coalesce(body, ''));

-- Or use a generated column (PostgreSQL 12+)
ALTER TABLE articles ADD COLUMN search_vector tsvector
    GENERATED ALWAYS AS (
        to_tsvector('english', coalesce(title, '') || ' ' || coalesce(body, ''))
    ) STORED;
```

### Index Types

| Index | When to Use | Pros | Cons |
|-------|-------------|------|------|
| **GiST** | General full-text search | Faster inserts, less storage | Slower reads than GIN |
| **GIN** | High-read, low-write | Fastest reads, concurrent updates OK | Larger index, slower builds |

```sql
-- GIN (preferred for most read-heavy workloads)
CREATE INDEX idx_articles_search ON articles USING GIN(search_vector);

-- GiST (better for frequent inserts)
CREATE INDEX idx_articles_search ON articles USING GIST(search_vector);
```

### Querying

```sql
-- Basic match
SELECT title FROM articles
WHERE search_vector @@ to_tsquery('english', 'search & algorithm');

-- Ranked results
SELECT title, ts_rank(search_vector, to_tsquery('english', 'search & algorithm')) AS rank
FROM articles
WHERE search_vector @@ to_tsquery('english', 'search & algorithm')
ORDER BY rank DESC;

-- Highlight matches
SELECT title, ts_headline('english', body, to_tsquery('english', 'search & algorithm'))
FROM articles
WHERE search_vector @@ to_tsquery('english', 'search & algorithm');
```

### Ranking Weights

```sql
-- Assign different weights to title vs body
ALTER TABLE articles ADD COLUMN search_vector tsvector
    GENERATED ALWAYS AS (
        setweight(to_tsvector('english', coalesce(title, '')), 'A') ||
        setweight(to_tsvector('english', coalesce(body, '')), 'B')
    ) STORED;

-- Weighted ranking (A=1.0, B=0.4, C=0.2, D=0.1 by default)
SELECT ts_rank('{0.1, 0.2, 0.4, 1.0}', search_vector, query);
```

## Other Full-Text Search Solutions

| Solution | Type | Best For |
|----------|------|----------|
| **PostgreSQL FTS** | Built-in | Simple, no external deps |
| **Elasticsearch** | Dedicated search engine | Large scale, complex queries |
| **Meilisearch** | Lightweight search | Typo-tolerant, fast setup |
| **SQLite FTS5** | Embedded | Mobile, local-first apps |

## Limitations

- Language-specific (stemming depends on dictionary)
- No typo tolerance out of the box (use `trigram` extension or Elasticsearch)
- No learning/ranking from user behavior

**Links**: [[PostgreSQL Features]] | [[PostgreSQL Extensions]] | [[Search Engines]] | [[Database Indexing Deep Dive]] | [[SQL Query Optimization]]
