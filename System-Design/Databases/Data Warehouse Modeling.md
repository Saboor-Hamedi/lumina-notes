---
id: a1b2c3d4-1157-4000-8000-000000000157
title: Data Warehouse Modeling
language: markdown
tags: [system-design, databases, data-warehouse, modeling]
selection: null
isPinned: false
timestamp: 1781500001157
---

**Links**: [[Snowflake and Data Warehousing]] | [[ETL and Data Pipeline Patterns]] | [[Data Engineering]] | [[ClickHouse]] | [[DuckDB]] | [[Delta Lake and Apache Iceberg]]


# Data Warehouse Modeling

Data warehouse modeling structures data for analytical querying. The three main approaches are Kimball (dimensional), Inmon (normalized), and Data Vault.

## Kimball Dimensional Modeling

Star schema: fact tables surrounded by dimension tables.

```
                    ┌──────────┐
                    │  Date    │
                    │  Dim     │
                    └────┬─────┘
                         │
┌──────────┐    ┌────────▼────────┐    ┌──────────┐
│ Customer │────│    Sales        │────│ Product  │
│ Dim      │    │    Fact         │    │ Dim      │
└──────────┘    └────────┬────────┘    └──────────┘
                         │
                    ┌────▼─────┐
                    │  Store   │
                    │  Dim     │
                    └──────────┘
```

**Fact table**: Measures (numeric), foreign keys to dimensions, grain-defining columns.

**Dimension table**: Descriptive attributes, textual context, slowly changing.

```sql
CREATE TABLE fact_sales (
    date_key       INT NOT NULL,
    customer_key   INT NOT NULL,
    product_key    INT NOT NULL,
    store_key      INT NOT NULL,
    quantity       INT NOT NULL,
    unit_price     DECIMAL(10,2) NOT NULL,
    discount       DECIMAL(5,2),
    total_amount   DECIMAL(10,2)
);

CREATE TABLE dim_customer (
    customer_key    INT PRIMARY KEY,
    customer_id     VARCHAR(20) NOT NULL,
    name            VARCHAR(100),
    email           VARCHAR(200),
    address         VARCHAR(500),
    city            VARCHAR(100),
    state           VARCHAR(50),
    zip             VARCHAR(10),
    created_date    DATE,
    version         INT,
    effective_from  DATE,
    effective_to    DATE,
    is_current      BOOLEAN
);
```

## Star Schema vs Snowflake Schema

| Aspect | Star | Snowflake |
|--------|------|-----------|
| Normalization | Denormalized | Normalized |
| Query complexity | Simple (fewer joins) | More joins |
| Storage | More (duplication) | Less |
| Maintenance | Simpler | More complex |
| Query performance | Faster | Slower |

## Snowflake Schema

Normalizes dimensions into sub-dimensions:

```
┌──────────┐     ┌──────────────┐
│ Category │─────│  Product     │
└──────────┘     │  Dim         │
                 └──────┬───────┘
                        │
┌──────────┐    ┌───────▼───────┐    ┌──────────┐
│ Customer │────│    Sales      │────│  Date    │
│ Dim      │    │    Fact       │    │  Dim     │
└──────────┘    └───────┬───────┘    └──────────┘
                        │
                  ┌─────▼─────┐
                  │  Store    │
                  │  Dim      │
                  └─────┬─────┘
                        │
                  ┌─────▼─────┐
                  │  Region   │
                  │  Dim      │
                  └───────────┘
```

## Data Vault Modeling

Separates data into hubs (business keys), links (relationships), and satellites (attributes).

```
┌────────────┐     ┌─────────────┐     ┌────────────┐
│ Hub_Customer │     │ Link_Order    │     │ Hub_Product │
│ CustomerID   │────▶│ CustomerID   │◀────│ ProductID   │
│ LoadDate     │     │ ProductID   │     │ LoadDate    │
│ RecordSource │     │ OrderID      │     │ RecordSource│
└────────────┘     │ LoadDate     │     └────────────┘
                    │ RecordSource │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │ Sat_Order   │
                    │ OrderID     │
                    │ LoadDate    │
                    │ Amount      │
                    │ Status      │
                    │ HashDiff    │
                    └─────────────┘
```

| Component | Purpose | Grain |
|-----------|---------|-------|
| Hub | Business key, unique | One row per business key |
| Link | Relationship between hubs | One row per relationship |
| Satellite | Descriptive attributes | One row per hub version |

## Inmon Approach

Corporate information factory: normalized (3NF) data warehouse with subject-oriented data marts.

```
Operational Systems → ETL → 3NF Enterprise Data Warehouse → Data Marts → BI Tools
```

**Pros**: Single source of truth, less redundancy, easier to maintain.

**Cons**: More complex queries, harder to understand for business users.

## Fact Table Types

| Type | Description | Example |
|------|-------------|---------|
| Transaction | One row per event | Sales transaction |
| Periodic Snapshot | One row per period | Monthly inventory |
| Accumulating Snapshot | One row per process lifecycle | Order fulfillment |
| Factless | No measures, just dimensions | Student attendance |

## Grain Declaration

The grain defines what one row represents:

```
Sales fact grain: One row per product sold in a transaction line item.

If grain changes:
  - Add row (if you need to store more detail)
  - Aggregate (if you need higher level summary)
  - DO NOT mix grains in one fact table
```

## Bridge Tables

Resolve many-to-many relationships between facts and dimensions:

```sql
-- Many products can belong to many categories
CREATE TABLE bridge_product_category (
    product_key     INT NOT NULL,
    category_key    INT NOT NULL,
    weight_factor   DECIMAL(5,2) NOT NULL DEFAULT 1.0
);
```

## Common Modeling Mistakes

| Mistake | Fix |
|---------|-----|
| Date/time in fact instead of dim | Use date dimension key |
| Degenerate dimension in fact only | Create degenerate dimension or keep as attribute |
| Over-normalizing dimensions | Denormalize for star schema |
| Mixing grains in one fact | Split into separate fact tables |
| No surrogate keys | Always use surrogate keys for dimension references |
| Character type for numeric IDs | Use integer surrogate keys |
