---
id: a1b2c3d4-0002-4000-8000-000000000002
title: Docker Containers
language: markdown
tags: [devops, containers, docker]
selection: null
isPinned: false
timestamp: 1781400000002
---
# Docker Containers

Docker packages applications and their dependencies into lightweight, portable containers that run consistently across environments.

## Concepts

| Term | Meaning |
|------|---------|
| Image | Read-only template with instructions |
| Container | Runnable instance of an image |
| Dockerfile | Script to build an image |
| Volume | Persistent data storage |
| Network | Communication between containers |

## Dockerfile Example

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["uvicorn", "main:app", "--host", "0.0.0.0"]
```

## Common Commands

```bash
docker build -t myapp .
docker run -p 8000:8000 myapp
docker compose up -d    # Multi-container
```

## Orchestration

- **Docker Compose**: Local multi-container setup
- **Kubernetes**: Production container orchestration at scale

**See also**: [[Dev Environment Setup]], [[Code Architecture Patterns]], [[Developer Workflow Automation]]