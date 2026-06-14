---
id: dev-062-0000-0000-0000-000000000048
title: Database Connection Pooling
language: markdown
tags: [system-design, databases, connection-pooling]
selection: null
isPinned: false
timestamp: 1781800000026
---
# Database Connection Pooling

**Links**: [[PostgreSQL Features]] | [[SQL Query Optimization]] | [[Database Engines Compared]] | [[Web Security]] | [[Caching Strategies]]

## What is Connection Pooling?

Connection pooling maintains a cache of database connections that can be reused, avoiding the overhead of creating new connections for each request.

## Without Pooling

```
Request 1 ──→ CREATE CONNECTION ──→ Query ──→ DESTROY ──  (slow)
Request 2 ──→ CREATE CONNECTION ──→ Query ──→ DESTROY ──  (slow)
Request 3 ──→ CREATE CONNECTION ──→ Query ──→ DESTROY ──  (slow)
```

## With Pooling

```
          ┌──── Pool ────┐
Request 1 ─┤  [Conn] [Conn] ├─── Query ──→ Return to pool  (fast)
Request 2 ─┤  [Conn]       ├─── Query ──→ Return to pool  (fast)
Request 3 ─┤  [Conn]       ├─── Query ──→ Return to pool  (fast)
          └──────────────┘
```

## Python (psycopg2 + pool)

```python
from psycopg2 import pool

connection_pool = pool.SimpleConnectionPool(
    minconn=2,
    maxconn=10,
    host="localhost",
    database="mydb",
    user="app",
    password="secret"
)

def query(sql, params):
    conn = connection_pool.getconn()
    try:
        with conn.cursor() as cur:
            cur.execute(sql, params)
            return cur.fetchall()
    finally:
        connection_pool.putconn(conn)
```

## PgBouncer (Dedicated Pooler)

PostgreSQL's dedicated connection pooler, running as a separate process.

```ini
[databases]
mydb = host=localhost port=5432 dbname=mydb

[pgbouncer]
listen_addr = 0.0.0.0
listen_port = 6432
pool_mode = transaction   # session, transaction, or statement
max_client_conn = 100
default_pool_size = 20
```

## Pool Size Guidelines

| Formula | Recommended |
|---------|-------------|
| `(core_count × 2) + effective_disk_count` | 10-30 connections |
| Too large | Context switching, connection storms |

**Next**: [[Database Backup Strategies]] — Protect your data