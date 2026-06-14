---
id: 23c1b7e9-6db5-46a0-8373-b9bcd31c7e4c
title: Developer Workflow Automation
language: markdown
tags: [devops, infrastructure, workflow-automation]
selection: null
isPinned: true
timestamp: 1781225071120
---

**Links**: [[Dev Environment Setup]] | [[Shell Scripting]] | [[Build Tools]] | [[CI CD Pipelines]] | [[Ansible]] | [[Package Managers]]

# Developer Workflow Automation

Automating repetitive development tasks boosts productivity and reduces errors. From git hooks to CI/CD pipelines, automation is a cornerstone of modern software engineering.

## CI/CD Pipeline Flow

```mermaid
graph LR
    Push["Code Push"] --> Lint["Lint & Format"]
    Lint --> Test["Unit Tests"]
    Test --> Build["Build / Compile"]
    Build --> Integration["Integration Tests"]
    Integration --> Deploy["Deploy Staging"]
    Deploy --> E2E["E2E Tests"]
    E2E --> Release["Release Production"]
```

## Git Hooks

Git hooks are scripts that run automatically on git events (pre-commit, pre-push, etc.).

### Client-Side Hooks

| Hook | Trigger | Use Case |
|------|---------|----------|
| pre-commit | Before commit message | Lint, format, secret scan |
| prepare-commit-msg | Before editor opens | Add branch name, ticket ID |
| commit-msg | After commit message | Validate commit format |
| pre-push | Before pushing | Run full test suite |

```bash
# .git/hooks/pre-commit — Run linters before committing
#!/bin/sh
ruff check . || exit 1
```

### Server-Side Hooks

| Hook | Use Case |
|------|----------|
| pre-receive | Block bad commits on server |
| update | Per-branch policies |
| post-receive | Deploy, notify CI |

## CI/CD Pipeline (GitHub Actions)

```yaml
name: CI
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: pip install -r requirements.txt
      - run: pytest
      - run: ruff check .
```

## Task Runners

| Tool | Config | Best For |
|------|--------|----------|
| **Make** | Makefile | Multi-language, simple targets |
| **npm scripts** | package.json | JavaScript/TypeScript projects |
| **Taskfile** | Taskfile.yml | Modern YAML, cross-platform |
| **Just** | justfile | Rust-inspired, simple syntax |
| **Invoke** | tasks.py | Python projects |

```makefile
# Makefile
.PHONY: test lint build
test:
	pytest
lint:
	ruff check .
build:
	docker build -t myapp .
```

## Pre-commit Framework

Centralize hooks for all team members:

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.4.0
    hooks:
      - id: ruff
  - repo: https://github.com/pre-commit/mirrors-prettier
    rev: v3.1.0
    hooks:
      - id: prettier
```

## Automation Best Practices

- Run fast checks (lint, format) in pre-commit hooks
- Run slow checks (integration tests) in CI
- Use pre-commit framework for team consistency
- Keep scripts idempotent (safe to re-run)
- Pin tool versions for reproducibility

**See also**: [[Git Version Control]], [[Dev Environment Setup]], [[Unit Testing Guide]]
