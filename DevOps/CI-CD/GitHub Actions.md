---
id: dev-065-0000-0000-0000-000000000054
title: GitHub Actions
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781900000006
---
# GitHub Actions

**Links**: [[CI CD Pipelines]] | [[Docker Compose]] | [[Git Workflows]] | [[Git Pull Requests]] | [[Unit Testing Guide]]

## What is GitHub Actions?

GitHub Actions is a CI/CD platform that automates build, test, and deployment workflows directly from your GitHub repository.

## Workflow Structure

```
.github/workflows/
└── ci.yml
```

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_PASSWORD: test
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
          cache: 'pip'
      - run: pip install -r requirements.txt
      - run: pip install -r requirements-dev.txt
      - run: ruff check .
      - run: pytest --cov=app --cov-report=xml
      - uses: codecov/codecov-action@v3
```

## Deployment

```yaml
deploy:
  needs: test
  if: github.ref == 'refs/heads/main'
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - run: |
        docker build -t myapp .
        docker tag myapp ghcr.io/${{ github.repository }}:latest
        docker push ghcr.io/${{ github.repository }}:latest
    - run: |
        echo "${{ secrets.SSH_KEY }}" > key.pem
        ssh -i key.pem user@server 'docker compose pull && docker compose up -d'
```

## Common Actions

```yaml
- uses: actions/checkout@v4           # Checkout code
- uses: actions/setup-python@v5       # Setup Python
- uses: actions/setup-node@v4         # Setup Node
- uses: actions/cache@v4              # Cache dependencies
- uses: docker/login-action@v3        # Login to registry
- uses: actions/upload-artifact@v4    # Upload build artifacts
```

## Secrets and Variables

```yaml
jobs:
  build:
    steps:
      - run: echo "Deploying to ${{ vars.ENVIRONMENT }}"
        env:
          API_KEY: ${{ secrets.API_KEY }}
```

## Matrix Builds

```yaml
strategy:
  matrix:
    python-version: ['3.11', '3.12']
    os: [ubuntu-latest, windows-latest]
```

**Next**: [[Docker Compose]] — Multi-container Docker