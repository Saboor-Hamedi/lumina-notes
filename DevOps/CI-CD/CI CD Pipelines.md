---
id: a1b2c3d4-0010-4000-8000-000000000010
title: CI CD Pipelines
language: markdown
tags: [devops, ci-cd, pipelines]
selection: null
isPinned: false
timestamp: 1781400000010
---
# CI CD Pipelines

Continuous Integration and Continuous Delivery automate building, testing, and deploying software.

## Pipeline Stages

```
Code Push → Lint → Test → Build → Stage → Deploy
```

## CI/CD with GitHub Actions

```yaml
name: CI/CD
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - run: pip install -r requirements.txt
      - run: ruff check .
      - run: pytest --cov

  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: docker build -t myapp .
      - run: docker push myapp:latest
```

## Best Practices

- Fail fast: run fastest checks first
- Keep pipelines under 10 minutes
- Use cached dependencies
- Pin tool versions for reproducibility
- Deploy from a single immutable artifact

**See also**: [[Developer Workflow Automation]], [[Docker Containers]], [[Unit Testing Guide]]

**Links**: [[GitHub Actions]] | [[GitLab CI]] | [[Jenkins]]
