---
tags: [data-engineering, schema-evolution, avro, protobuf, iceberg]
---

# 08 — Schema Evolution

## Avro Schema Evolution

```avro
// Schema V1 (original)
{
  "type": "record", "name": "Order",
  "fields": [
    {"name": "order_id", "type": "string"},
    {"name": "customer_id", "type": "string"},
    {"name": "amount", "type": "double"},
    {"name": "order_date", "type": "string"}
  ]
}

// Schema V2 (backward compatible)
{
  "type": "record", "name": "Order",
  "fields": [
    {"name": "order_id", "type": "string"},
    {"name": "customer_id", "type": "string"},
    {"name": "amount", "type": "double"},
    {"name": "order_date", "type": "string"},
    {"name": "currency", "type": "string", "default": "USD"},
    {"name": "discount", "type": ["null", "double"], "default": null}
  ]
}
```

## Protocol Buffers

```protobuf
message Order {
  string order_id = 1;
  string customer_id = 2;
  double amount = 3;
  string order_date = 4;
  string currency = 5;          // Added — new field number
  optional double discount = 6;  // Optional field
  enum Status {
    STATUS_UNSPECIFIED = 0;
    PENDING = 1; COMPLETED = 2; CANCELLED = 3;
  }
  Status status = 7;
  repeated LineItem items = 8;
}
```

## Iceberg Table Evolution

```sql
-- Add column
ALTER TABLE catalog.sales.orders ADD COLUMN currency STRING AFTER amount;

-- Add nested column
ALTER TABLE catalog.sales.orders
ADD COLUMN shipping.address STRUCT<street: STRING, city: STRING, zip: STRING>;

-- Rename column
ALTER TABLE catalog.sales.orders RENAME COLUMN customer_id TO buyer_id;

-- Widen column type
ALTER TABLE catalog.sales.orders ALTER COLUMN amount TYPE DECIMAL(12,2);

-- Drop column
ALTER TABLE catalog.sales.orders DROP COLUMN temporary_flag;

-- Partition evolution
ALTER TABLE catalog.sales.orders ADD PARTITION FIELD months(order_date);

-- Time travel
SELECT * FROM catalog.sales.orders FOR SYSTEM_VERSION AS OF 123456789;
SELECT * FROM catalog.sales.orders FOR TIMESTAMP AS OF '2024-06-01';
```

## Compatibility Rules

| Change | Avro (Backward) | Avro (Forward) | Protobuf | Iceberg |
|--------|----------------|----------------|----------|---------|
| Add field with default | ✅ | ✅ | ✅ | ✅ |
| Add field without default | ❌ | ✅ | ✅ | ✅ |
| Remove field | ✅ (if default) | ❌ | ❌ | ✅ |
| Rename field | ❌ | ❌ | ❌ | ✅ |
| Widen type | ✅ (int→long) | ✅ | ✅ | ✅ |
| Narrow type | ❌ | ❌ | ❌ | ❌ |
| Reorder fields | ✅ | ✅ | ✅ | ✅ |

**Links**: [[System-Design/Databases/Data Engineering/07 Real-Time Streaming]] | [[System-Design/Databases/Data Engineering/05 Data Quality & Cataloging]] | [[System-Design/Databases/Data Engineering/03 dbt]]
**See also**: [[Delta Lake and Apache Iceberg]], [[Stream Processing]]
