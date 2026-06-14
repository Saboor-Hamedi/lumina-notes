---
id: a1b2c3d4-1115-4000-8000-000000000115
title: DuckDB
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001115
---
# DuckDB

DuckDB is an embeddable, in-process SQL OLAP database designed for analytical workloads. It has no external dependencies and runs in-process with Python, R, or Node.js.

## Why DuckDB?

| Database | Use Case | Data Location |
|----------|----------|--------------|
| SQLite | OLTP, small data, embedded | Local file |
| PostgreSQL | OLTP, production server | Server |
| ClickHouse | OLAP, distributed analytics | Server cluster |
| DuckDB | OLAP, embedded analytics | Local file or in-memory |

**DuckDB**: OLAP power (vectorized execution, columnar storage) in a SQLite-like package (embedded, zero-config).

## Key Features

| Feature | Benefit |
|---------|---------|
| Columnar engine | Vectorized execution, analytical performance |
| Zero-config | No server, no config, no dependencies |
| Full SQL | Window functions, CTEs, complex joins, subqueries |
| Multi-threaded | Parallel query execution out of the box |
| Direct Parquet/CSV/JSON query | Query files without loading |
| Python integration | `duckdb.sql()`, pandas/numpy interop |
| In-memory + persistent | Both modes supported |

## Quick Start

```python
import duckdb

# In-memory database
result = duckdb.sql("SELECT 42 AS answer")
print(result)  # ┌────────┐
               # │ answer │
               # ├────────┤
               # │   42   │
               # └────────┘

# Query a CSV file directly
duckdb.sql("""
    SELECT year, SUM(revenue) as total
    FROM 'sales_*.csv'
    GROUP BY year
    ORDER BY year
""")

# Query Parquet files
result = duckdb.sql("""
    SELECT
        DATE_TRUNC('month', order_date) AS month,
        COUNT(*) AS orders,
        SUM(amount) AS revenue
    FROM 'data/orders/*.parquet'
    WHERE order_date >= '2025-01-01'
    GROUP BY month
    ORDER BY month
""").fetchdf()  # returns pandas DataFrame
```

## Python Integration

```python
import duckdb
import pandas as pd

# Connect to persistent database
conn = duckdb.connect('analytics.db')

# Create table from pandas
df = pd.read_csv('transactions.csv')
conn.execute("CREATE TABLE transactions AS SELECT * FROM df")

# Query with Python variable binding
threshold = 1000
conn.execute("""
    SELECT
        user_id,
        COUNT(*) as txn_count,
        SUM(amount) as total
    FROM transactions
    WHERE amount > ?
    GROUP BY user_id
    HAVING total > ?
    ORDER BY total DESC
""", [threshold, threshold * 10]).df()
```

## Advanced Analytics

```sql
-- Window functions
SELECT
    user_id,
    order_date,
    amount,
    SUM(amount) OVER (PARTITION BY user_id ORDER BY order_date) AS running_total,
    AVG(amount) OVER (PARTITION BY user_id ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg
FROM orders
ORDER BY user_id, order_date;

-- List and struct types (native support)
CREATE TABLE events (
    event_id INTEGER,
    tags VARCHAR[],  -- array
    metadata STRUCT(name VARCHAR, score DOUBLE)  -- struct
);

-- Recursive CTE
WITH RECURSIVE employee_tree AS (
    SELECT id, name, manager_id, 1 AS level
    FROM employees
    WHERE manager_id IS NULL
    UNION ALL
    SELECT e.id, e.name, e.manager_id, et.level + 1
    FROM employees e JOIN employee_tree et ON e.manager_id = et.id
)
SELECT * FROM employee_tree;
```

## Performance Characteristics

| Feature | DuckDB | SQLite |
|---------|--------|--------|
| Storage | Columnar | Row-oriented |
| Compression | Yes (light) | No |
| Parallel execution | Yes | No |
| Vectorized | Yes | No |
| Typical query speed | 10-100x faster | Baseline |
| Latency (first query) | 10-50ms | < 1ms |

## Use Cases

| Use Case | Why DuckDB |
|----------|------------|
| Embedded analytics | Runs inside Python process |
| ETL pipelines | Load, transform, output (zero config) |
| Data exploration | Interactive analysis of large CSV/Parquet |
| Reporting | Generate aggregations for BI dashboards |
| Local development | Full SQL without needing a database server |
| CI/CD testing | No server to set up in test pipelines |

## Extensions

```sql
-- Install extensions on demand
INSTALL httpfs;
LOAD httpfs;

-- Query S3 Parquet directly
SELECT station, AVG(temperature)
FROM read_parquet('s3://weather-data/2026/*.parquet')
GROUP BY station;

-- Full-text search
INSTALL fts;
LOAD fts;
PRAGMA create_fts_index('documents', 'content', 'content_idx');
```

**Links**: [[SQLite Reference]] | [[ClickHouse]] | [[Data Engineering]] | [[Python Imports and Modules]] | [[SQL Query Optimization]]
