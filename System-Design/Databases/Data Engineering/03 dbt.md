---
tags: [data-engineering, dbt, transformation]
---

# 03 — dbt (Data Build Tool)

## Project Structure

```
my_dbt_project/
├── models/
│   ├── staging/         (stg_orders.sql, stg_customers.sql)
│   ├── marts/core/      (dim_customers.sql, fct_orders.sql)
│   └── marts/finance/   (revenue_kpis.sql)
├── tests/               (custom tests)
├── macros/              (reusable SQL functions)
├── snapshots/           (SCD Type 2)
├── analyses/            (ad-hoc queries)
├── seeds/               (CSV reference data)
├── docs/                (documentation)
├── dbt_project.yml
└── profiles.yml         (DB connection)
```

## Sources

```yaml
sources:
  - name: raw_sales
    database: analytics_db
    schema: raw
    tables:
      - name: orders
        loaded_at_field: _loaded_at
        freshness:
          warn_after: {count: 6, period: hour}
          error_after: {count: 24, period: hour}
        columns:
          - name: order_id
            tests: [unique, not_null]
```

## Models — Staging

```sql
WITH source AS (
    SELECT * FROM {{ source('raw_sales', 'orders') }}
),
cleaned AS (
    SELECT
        order_id,
        customer_id,
        CAST(order_date AS DATE) AS order_date,
        COALESCE(amount, 0) AS amount
    FROM source
    WHERE order_id IS NOT NULL
)
SELECT * FROM cleaned
```

## Models — Marts (Fact & Dimension)

```sql
WITH customers AS (SELECT * FROM {{ ref('stg_customers') }}),
orders AS (SELECT * FROM {{ ref('stg_orders') }}),
customer_aggregates AS (
    SELECT customer_id, COUNT(*) AS total_orders, SUM(amount) AS lifetime_value
    FROM orders GROUP BY 1
)
SELECT c.*, ca.total_orders, ca.lifetime_value,
    CASE
        WHEN ca.total_orders >= 10 THEN 'VIP'
        WHEN ca.total_orders >= 5 THEN 'Regular'
        ELSE 'Occasional'
    END AS customer_tier
FROM customers c LEFT JOIN customer_aggregates ca ON c.customer_id = ca.customer_id
```

## Tests

```yaml
models:
  - name: fct_orders
    columns:
      - name: order_id
        tests: [unique, not_null]
      - name: amount
        tests: [not_null]
      - name: order_status
        tests:
          - accepted_values:
              values: ['completed', 'cancelled', 'refunded', 'pending']
```

## Snapshots (SCD Type 2)

```sql
{% snapshot scd_customers %}
{{
    config(target_database='analytics', target_schema='snapshots',
           strategy='timestamp', unique_key='customer_id', updated_at='updated_at')
}}
SELECT * FROM {{ source('raw_sales', 'customers') }}
{% endsnapshot %}
```

## Macros

```sql
{% macro generate_schema_name(custom_schema_name, node) -%}
    {%- if custom_schema_name is none -%}{{ target.schema }}
    {%- else -%}{{ custom_schema_name }}_{{ target.name }}
    {%- endif -%}
{%- endmacro %}
```

**Links**: [[System-Design/Databases/Data Engineering/04 Orchestration]] | [[System-Design/Databases/Data Engineering/05 Data Quality & Cataloging]] | [[System-Design/Databases/Data Engineering/02 Architecture & Medallion]]
**See also**: [[Apache Airflow]], [[Apache Spark]]
