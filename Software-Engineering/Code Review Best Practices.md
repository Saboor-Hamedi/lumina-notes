---
id: a1b2c3d4-0020-4000-8000-000000000020
title: Code Review Best Practices
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781400000020
---
# Code Review Best Practices

Code review is the systematic examination of code changes by peers to catch bugs, improve quality, and share knowledge.

## What to Look For

| Category | Questions |
|----------|-----------|
| Correctness | Does the code do what it's supposed to? |
| Edge Cases | What happens with empty/null/malformed input? |
| Security | Are there injection risks, exposed secrets? |
| Performance | Are there N+1 queries, unnecessary allocations? |
| Readability | Is the intent clear? Are names descriptive? |
| Test Coverage | Are there tests for the new logic? |

## Review Etiquette

- **Author**: Keep PRs small (< 400 lines). Write a clear description.
- **Reviewer**: Be specific and kind. Ask questions, don't dictate.
- **Both**: Respond within 24 hours. Assume good intent.

## Checklist

- [ ] Code follows project conventions
- [ ] No commented-out code or debug logs
- [ ] Error handling is appropriate
- [ ] Documentation is updated
- [ ] Tests pass and cover new code
- [ ] No security vulnerabilities introduced

**See also**: [[Git Version Control]], [[Unit Testing Guide]], [[Developer Workflow Automation]], [[Software Design Principles]]