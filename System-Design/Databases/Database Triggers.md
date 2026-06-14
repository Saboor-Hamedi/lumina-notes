---
id: db-026-0000-0000-0000-000000000050
title: Database Triggers
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781900000002
---
# Database Triggers

**Links**: [[PostgreSQL Features]] | [[Database Views]] | [[Full-Text Search]] | [[Database Security]] | [[Database Transactions]]

## What is a Trigger?

A trigger runs a function automatically when a specified event occurs on a table.

## Create Trigger

```sql
-- 1. Create the trigger function
CREATE FUNCTION log_user_changes()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO audit_log (table_name, action, old_data, new_data, changed_by)
    VALUES (
        'users',
        TG_OP,
        row_to_json(OLD),
        row_to_json(NEW),
        current_user
    );
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- 2. Create the trigger
CREATE TRIGGER users_audit
    AFTER INSERT OR UPDATE OR DELETE ON users
    FOR EACH ROW
    EXECUTE FUNCTION log_user_changes();
```

## Trigger Timing

| Timing | Description |
|--------|-------------|
| BEFORE | Run before the operation (modify/validate data) |
| AFTER | Run after the operation (logging, cascading) |
| INSTEAD OF | Replace the operation (views) |

## Event Types

```sql
CREATE TRIGGER trigger_name
    { BEFORE | AFTER | INSTEAD OF }
    { INSERT | UPDATE | DELETE | TRUNCATE }
    ON table_name
    [FOR EACH { ROW | STATEMENT }]
    EXECUTE FUNCTION function_name();
```

## Row vs Statement Triggers

| Trigger | Runs | Access |
|---------|------|--------|
| FOR EACH ROW | Once per affected row | OLD and NEW values |
| FOR EACH STATEMENT | Once per SQL statement | No row access |

## Use Cases

- **Audit logging**: Track all changes to sensitive tables
- **Validation**: Enforce complex business rules
- **Derived data**: Auto-update summary columns
- **Soft delete**: Set deleted_at instead of deleting
- **Search indexing**: Auto-update tsvector columns

## Caution

- Triggers can slow down writes
- Hidden logic — debugging surprise
- Recursive triggers (trigger fires trigger)
- Test thoroughly

**Next**: [[Database Views]] — Virtual tables