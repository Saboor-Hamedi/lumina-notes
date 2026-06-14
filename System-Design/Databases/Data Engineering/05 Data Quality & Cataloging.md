---
tags: [data-engineering, data-quality, cataloging, datahub]
---

# 05 — Data Quality & Cataloging

## Data Quality

```yaml
expectations:
  - table: dim_customers
    constraints:
      - unique: customer_id
      - not_null: [customer_id, email, region]
      - pattern:
          column: email
          regex: ^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}$
  - table: fct_orders
    constraints:
      - unique: order_id
      - not_null: [order_id, customer_id, amount]
      - range:
          column: amount
          min: 0
          max: 100000
    freshness:
      - table: stg_orders
        timestamp_column: _loaded_at
        max_age: 6h
```

### Great Expectations

```python
import great_expectations as ge
df_ge = ge.dataset.PandasDataset(df)
df_ge.expect_column_values_to_not_be_null('customer_id')
df_ge.expect_column_values_to_be_between('amount', 0, 100000)
df_ge.expect_table_row_count_to_be_between(min_value=100, max_value=1000000)
results = df_ge.validate()
```

## Data Cataloging

| Tool | Open Source | Search | Lineage | Key Features |
|------|-------------|--------|---------|--------------|
| **DataHub** | Yes | Elasticsearch | Column-level | Real-time metadata, GraphQL API |
| **Amundsen** | Yes | Elasticsearch | Table-level | Good search, badges |
| **Atlan** | No | AI-powered | Column-level | Collaboration, playbooks |
| **Apache Atlas** | Yes | Solr | Column-level | Hadoop integration |
| **Marquez** | Yes | Basic | Column-level | Focus on lineage |

### DataHub Ingestion Example

```yaml
source:
  type: dbt
  config:
    manifest_path: ./target/manifest.json
    catalog_path: ./target/catalog.json
    target_platform: snowflake

source:
  type: snowflake
  config:
    username: $SNOWFLAKE_USER
    password: $SNOWFLAKE_PASSWORD
    account_id: my_account
    database: ANALYTICS

sink:
  type: datahub-rest
  config:
    server: http://localhost:8080
```

**Links**: [[System-Design/Databases/Data Engineering/03 dbt]] | [[System-Design/Databases/Data Engineering/04 Orchestration]] | [[System-Design/Databases/Data Engineering/08 Schema Evolution]]
**See also**: [[Delta Lake and Apache Iceberg]]
