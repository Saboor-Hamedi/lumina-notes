---
id: db-027-0000-0000-0000-000000000051
title: Database Migration Tools
language: markdown
tags:
  - system-design
  - databases
  - migration
  - schema
selection: null
isPinned: false
timestamp: 1781701191394
---
# Database Migration Tools

**Links**: [[PostgreSQL Features]] | [[Database Backup Strategies]] | [[CI CD Pipelines]] | [[Database Triggers]] | [[Database Views]]

## What are Migrations?

Migrations are version-controlled scripts that evolve your database schema over time. They enable reproducible, reversible schema changes.

## Alembic (Python)

```python
# migrations/versions/0001_create_users.py
from alembic import op
import sqlalchemy as sa

def upgrade():
    op.create_table(
        'users',
        sa.Column('id', sa.Integer(), primary_key=True),
        sa.Column('name', sa.String(100), nullable=False),
        sa.Column('email', sa.String(255), unique=True),
    )

def downgrade():
    op.drop_table('users')
```

```bash
alembic init migrations          # Initialize
alembic revision --autogenerate  # Auto-detect changes
alembic upgrade head             # Apply all pending
alembic downgrade -1             # Rollback one step
```

## Flyway (Java, but DB-agnostic)

```sql
-- V1__create_users.sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL
);
```

```bash
flyway migrate
flyway info
flyway undo
```

## Features

| Feature | Alembic | Flyway |
|---------|---------|--------|
| Language | Python | Java (CLI available) |
| Auto-generation | Yes (SQLAlchemy) | No |
| Downgrade | Yes | Paid version |
| Repeatable migrations | Yes | Yes |
| CI/CD friendly | Yes | Yes |

## Best Practices

- Always test migrations on staging first
- Write both upgrade AND downgrade
- Keep migrations small and focused
- Never edit existing migrations after merging
- Use transactions for migration safety

**Next**: [[Database Triggers]] — Automated database actions
