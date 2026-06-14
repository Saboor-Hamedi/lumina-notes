---
id: db-025-0000-0000-0000-000000000049
title: Database Views
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781900000001
---
# Database Views

**Links**: [[SQL Query Optimization]] | [[Common Table Expressions]] | [[PostgreSQL Features]] | [[Database Security]] | [[Full-Text Search]]

## What is a View?

A view is a saved SQL query that behaves like a virtual table. It doesn't store data — it runs the underlying query when accessed.

## Basic View

```sql
CREATE VIEW active_customers AS
SELECT id, name, email, created_at
FROM customers
WHERE is_active = true
AND deleted_at IS NULL;

-- Query the view like a table
SELECT * FROM active_customers WHERE created_at > '2024-01-01';
```

## View vs Table

| Aspect | View | Table |
|--------|------|-------|
| Storage | None (runs query) | Stores data |
| Performance | Runs query each time | Direct access |
| Data freshness | Always current | As inserted |
| Indexable | No (use materialized) | Yes |
| Updatable | Simple views only | Yes |

## Updatable Views

Simple views on single tables can be updated:

```sql
CREATE VIEW active_users AS
SELECT id, name, email FROM users WHERE active = true;

-- This works if the view is "simple enough"
INSERT INTO active_users (name, email) VALUES ('Alice', 'alice@example.com');
UPDATE active_users SET email = 'new@example.com' WHERE id = 1;
```

## Materialized Views

Store the query result on disk for faster access:

```sql
CREATE MATERIALIZED VIEW monthly_sales AS
SELECT
    DATE_TRUNC('month', order_date) AS month,
    SUM(amount) AS total,
    COUNT(*) AS orders
FROM orders
GROUP BY 1
ORDER BY 1;

-- Refresh
REFRESH MATERIALIZED VIEW monthly_sales;
```

## Use Cases

- **Security**: Hide sensitive columns from certain users
- **Simplicity**: Pre-join complex queries
- **Compatibility**: Match legacy interface without changing tables
- **Reporting**: Materialized views for dashboards

**Next**: [[Database Triggers]] — Automated database actions