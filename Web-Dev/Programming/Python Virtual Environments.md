---
id: a1b2c3d4-0019-4000-8000-000000000019
title: Python Virtual Environments
language: markdown
tags: [web-dev, programming, python, virtual-environments]
selection: null
isPinned: false
timestamp: 1781400000019
---
# Python Virtual Environments

Virtual environments isolate Python dependencies per project, preventing version conflicts.

## Tools Compared

| Tool | Speed | Lock File | Features |
|------|-------|-----------|----------|
| venv | Fast | No | Built-in, minimal |
| pipenv | Slow | Pipfile.lock | Combines pip + virtualenv |
| Poetry | Medium | poetry.lock | Build system + deps |
| uv | Very fast | uv.lock | Rust-based, drop-in pip replacement |

## Basic venv Workflow

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
deactivate
```

## Best Practices

- Always use a virtual environment per project
- Add `.venv/` to `.gitignore`
- Pin exact versions in requirements.txt or lock files

**See also**: [[Dev Environment Setup]], [[Docker Containers]], [[Git Version Control]]