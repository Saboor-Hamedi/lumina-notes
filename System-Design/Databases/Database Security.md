---
id: new-010-0000-0000-0000-000000000010
title: Database Security
language: markdown
tags: [system-design, databases, database-security]
selection: null
isPinned: false
timestamp: 1781600000010
---
# Database Security

**Links**: [[Web Security]] | [[PostgreSQL Features]] | [[Database Engines Compared]] | [[Environment Variables]] | [[Database Transactions]]

## Threat Model

| Threat | Impact | Mitigation |
|--------|--------|------------|
| SQL Injection | Data theft/loss | Parameterized queries |
| Unauthorized access | Data breach | Authentication + authorization |
| Data at rest exposure | Compliance violation | Encryption |
| Man-in-the-middle | Credential theft | TLS/SSL |
| Insider threat | Data exfiltration | Audit logging |

## SQL Injection Prevention

```python
# WRONG: String interpolation (vulnerable!)
query = f"SELECT * FROM users WHERE email = '{email}'"

# CORRECT: Parameterized query
cursor.execute("SELECT * FROM users WHERE email = %s", (email,))
```

## Authentication

```sql
-- PostgreSQL: create user with limited privileges
CREATE USER app_user WITH PASSWORD 'strong_password';
GRANT SELECT, INSERT, UPDATE ON ALL TABLES IN SCHEMA public TO app_user;
GRANT USAGE ON ALL SEQUENCES IN SCHEMA public TO app_user;
```

## Encryption at Rest

```sql
-- PostgreSQL: encrypt a column with pgcrypto
CREATE EXTENSION IF NOT EXISTS pgcrypto;

UPDATE users SET credit_card = pgp_sym_encrypt(
  '4111-1111-1111-1111',
  current_setting('app.encryption_key')
);
```

## Connection Security

```ini
# Always use SSL for remote connections
# postgresql.conf
ssl = on
ssl_cert_file = 'server.crt'
ssl_key_file = 'server.key'

# Connection string
postgresql://user:password@host:5432/db?sslmode=require
```

## Audit Logging

```sql
-- Enable PostgreSQL audit
CREATE EXTENSION pgaudit;
SET pgaudit.log = 'write,ddl';
```

## Best Practices

- Least privilege principle — grant minimum required permissions
- Rotate credentials regularly
- Encrypt sensitive columns
- Use network isolation (private subnets, security groups)
- Regular backup with encryption
- Monitor failed authentication attempts

**Next**: [[Rate Limiting]] — Protect your APIs