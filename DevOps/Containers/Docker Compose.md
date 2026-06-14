---
id: dev-064-0000-0000-0000-000000000053
title: Docker Compose
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781900000005
---
# Docker Compose

**Links**: [[Docker Containers]] | [[Kubernetes Basics]] | [[CI CD Pipelines]] | [[Dev Environment Setup]] | [[Microservices Architecture]]

## What is Docker Compose?

Docker Compose defines and runs multi-container applications using a YAML file. It's ideal for local development and testing.

## docker-compose.yml

```yaml
version: '3.9'

services:
  app:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DB_HOST=db
      - DB_NAME=myapp
      - DEBUG=true
    depends_on:
      - db
    volumes:
      - .:/app
      - /app/__pycache__

  db:
    image: postgres:16
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  pgdata:
```

## Commands

```bash
docker compose up -d           # Start all services
docker compose down            # Stop and remove
docker compose logs -f         # Follow logs
docker compose ps              # List services
docker compose exec app bash   # Shell into service
docker compose build           # Rebuild images
docker compose pull            # Pull latest images
```

## Profiles

```yaml
services:
  app:
    image: myapp
  db:
    image: postgres
  monitoring:
    image: grafana/grafana
    profiles: ["monitoring"]    # Only starts with profile
```

```bash
docker compose --profile monitoring up -d
```

## Multi-Stage Build

```yaml
services:
  app:
    build:
      context: .
      target: production   # Use specific build stage
```

## Health Checks

```yaml
services:
  db:
    healthcheck:
      test: ["CMD-SHELL", "pg_isready"]
      interval: 5s
      timeout: 5s
      retries: 5
```

**Next**: [[GitHub Actions]] — CI/CD workflows