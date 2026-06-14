---
id: a1b2c3d4-1005-4000-8000-000000000005
title: Clean Code Principles
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001005
---
# Clean Code Principles

Clean code is readable, maintainable, and does what it says. It follows conventions, minimizes surprise, and is easy to change.

## Naming

| Principle | Good | Bad |
|-----------|------|-----|
| Reveal intent | `userCount` | `x` |
| Pronounceable | `createdAt` | `crDt` |
| Searchable | `MAX_RETRY_COUNT` | `5` |
| No disinformation | `accountList` (if not a List) | `accounts` (fine) |
| One word per concept | `fetch`, `get`, `retrieve` — pick one | Mixing them |

## Functions

- **Small**: Functions should do one thing (3-20 lines)
- **Single Level of Abstraction**: Don't mix high and low level in one function
- **No side effects**: Don't modify state unexpectedly
- **Command-Query Separation**: Commands change state, queries return values
- **Fewer parameters**: 0-2 params ideal, 3+ suggests a struct

```python
# Clean
def get_active_users(database):
    return database.query("SELECT * FROM users WHERE active = true")

# Avoid
def process_and_update_and_notify(user_id, data, flag, callback):
    ...
```

## Comments

- Comments should explain **why**, not **what**
- Prefer expressive code over comments
- Avoid commented-out code
- Use docstrings for public APIs only

## Error Handling

- Use exceptions, not return codes
- Provide context in exception messages
- Don't return null — use Optional/Maybe patterns
- Fail fast: validate inputs early

## Formatting

- Consistent indentation (2 or 4 spaces)
- Vertical density: related concepts close together
- Blank lines separate concepts
- Consistent brace placement

## DRY, YAGNI, KISS

| Acronym | Meaning |
|---------|---------|
| DRY | Don't Repeat Yourself |
| YAGNI | You Ain't Gonna Need It |
| KISS | Keep It Simple, Stupid |

**Links**: [[Object-Oriented Programming]] | [[Functional Programming]] | [[Refactoring Techniques]] | [[Software Design Principles]] | [[Code Review Best Practices]]
