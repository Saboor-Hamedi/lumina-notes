---
id: a1b2c3d4-0018-4000-8000-000000000018
title: Environment Variables
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781400000018
---
# Environment Variables

Environment variables externalize configuration from code, enabling the same build to run across environments.

## Twelve-Factor App Config

Store config in environment variables, not in code.

```python
import os

DB_HOST = os.getenv("DB_HOST", "localhost")
DB_PORT = int(os.getenv("DB_PORT", "5432"))
DEBUG = os.getenv("DEBUG", "false").lower() == "true"
SECRET_KEY = os.getenv("SECRET_KEY")
```

## .env Files

```
# .env (never commit to git)
DB_HOST=prod-db.example.com
DB_PASSWORD=s3cret!
DEBUG=false
```

## Best Practices

- Never commit secrets to version control
- Use `.env.example` with dummy values as a template
- Validate all required variables at startup
- Use secret management tools (Vault, AWS Secrets Manager) in production

**See also**: [[Dev Environment Setup]], [[Docker Containers]], [[CI CD Pipelines]]