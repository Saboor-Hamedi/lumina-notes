---
id: a1b2c3d4-1036-4000-8000-000000000036
title: Technical Writing
language: markdown
tags: [software-engineering]
selection: null
isPinned: false
timestamp: 1781500001036
---
# Technical Writing

Technical writing communicates complex information clearly and concisely for a specific audience.

## Documentation Types

| Type | Audience | Purpose |
|------|----------|---------|
| Tutorial | New users | Step-by-step learning |
| How-To Guide | Users with goal | Solve a specific problem |
| Reference | All users | Authoritative details (API docs) |
| Explanation | Curious readers | Background and concepts |

**Diátaxis framework**: These four types form a complete documentation system.

## Writing Principles

| Principle | Bad | Good |
|-----------|-----|------|
| Active voice | "The button should be clicked" | "Click the button" |
| Concise | "In order to initiate the process of..." | "To start..." |
| Specific | "Wait a moment" | "Wait 10 seconds" |
| Task-oriented | "The configuration section" | "Configure the database" |
| Consistent | Two terms for one concept | Always use same term |

## Structure

```
# Title
Introduction (what, why, prerequisites)
## Step 1: ...
## Step 2: ...
## Summary
## Next Steps
```

## Code Examples

- Use real, runnable code
- Show input AND expected output
- Annotate important lines
- Never use placeholder "foo/bar" in examples

```python
# Calculate distance between two points
def distance(x1, y1, x2, y2):
    return ((x2 - x1)**2 + (y2 - y1)**2)**0.5

# Output: distance(0, 0, 3, 4) → 5.0
```

## API Documentation

| Element | Description |
|---------|-------------|
| Endpoint | URL path and HTTP method |
| Parameters | Query, path, header, body |
| Request body | Example JSON |
| Response | Status code, body, headers |
| Errors | Possible error codes and meanings |

```json
// Sample response for GET /api/users/:id
{
  "id": "usr_123",
  "name": "Alice",
  "email": "alice@example.com",
  "created_at": "2026-01-15T10:00:00Z"
}
```

## Readme Best Practices

- Project name and one-liner
- Quick start (install + run in 30 seconds)
- Prerequisites
- Configuration
- Examples
- Contributing guide
- License

## Style Guides

| Resource | For |
|----------|-----|
| Google Developer Documentation Style Guide | General tech docs |
| Microsoft Style Guide | Windows/.NET ecosystem |
| Apple Style Guide | Apple platform docs |
| GitHub Docs Style Guide | Open source docs |

## Tools

| Tool | Use |
|------|-----|
| Markdown | Simple formatting |
| Docusaurus | Documentation sites |
| Read the Docs | Hosted Sphinx docs |
| Swagger/OpenAPI | API documentation |
| Storybook | Component documentation |

**Links**: [[API Documentation]] | [[API Versioning]] | [[Programming Resources]] | [[Code Review Best Practices]] | [[Clean Code Principles]]
