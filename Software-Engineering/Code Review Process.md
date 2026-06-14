---
id: a1b2c3d4-1078-4000-8000-000000000078
title: Code Review Process
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001078
---
# Code Review Process

Code review is a systematic examination of code changes by peers. It catches bugs, improves design, shares knowledge, and enforces standards.

## Why Code Review?

| Benefit | Impact |
|---------|--------|
| Bug detection | Finds 35-70% of defects before merge |
| Knowledge sharing | Team learns from each other's code |
| Code quality | Enforces standards, best practices |
| Team ownership | Shared responsibility, no bus factor |
| Mentoring | Junior devs learn from senior feedback |
| Design improvement | Early feedback on architecture |

## Review Size

| Size | Lines Changed | Time to Review | Recommended? |
|------|--------------|----------------|-------------|
| Tiny | < 50 | 5-10 min | ✓ Best |
| Small | 50-200 | 15-30 min | ✓ Good |
| Medium | 200-400 | 30-60 min | ⚠ Split if possible |
| Large | 400-1000 | 60+ min | ✗ Too large |
| Massive | 1000+ | Hours | ✗ Split into multiple PRs |

## What to Look For

### Priority Order

| Priority | Area | Questions |
|----------|------|-----------|
| 1 | Functionality | Does the code work? Are there bugs? |
| 2 | Design | Is this the right approach? |
| 3 | Readability | Is the code easy to understand? |
| 4 | Standards | Follows team conventions? |
| 5 | Testing | Are there adequate tests? |
| 6 | Performance | Any obvious performance issues? |
| 7 | Security | Input validation, auth checks? |

## Code Review Etiquette

### For Authors

- Keep PRs small and focused (one concern per PR)
- Write clear title and description (what + why)
- Self-review before requesting reviews
- Respond to feedback promptly
- Explain tradeoffs in comments ("I chose X over Y because...")
- Don't take feedback personally

### For Reviewers

- Be respectful and constructive ("How about..." not "You should...")
- Explain reasoning behind suggestions
- Distinguish between blockers and nitpicks
- Approve when it's good enough (not perfection)
- Respond in a timely manner (< 24 hours)
- Ask questions instead of making demands

## The Review Mindset

```
╔══════════════════════════════════════════════╗
║  Review the code, not the person.            ║
║  Suggest improvements, don't demand them.    ║
║  Approve when "good enough," not "perfect."  ║
║  Assume good intent from both sides.         ║
╚══════════════════════════════════════════════╝
```

## Review Checklist

- [ ] Does the code satisfy acceptance criteria?
- [ ] Are there edge cases not handled?
- [ ] Is error handling appropriate?
- [ ] Are there tests for new functionality?
- [ ] Do existing tests still pass?
- [ ] Is the code following team conventions?
- [ ] Are there any security concerns?
- [ ] Is documentation updated?
- [ ] Are there TODO/FIXME comments that need action?
- [ ] Does the PR include unnecessary changes?

## Automated Review

| Category | Tool |
|----------|------|
| Linting | ESLint, Ruff, Rubocop |
| Formatting | Prettier, gofmt, rustfmt |
| Security | Semgrep, SonarQube, CodeQL |
| Tests | CI must pass |
| Coverage | Codecov, Coveralls |
| Complexity | SonarQube (cognitive complexity) |

## Handling Disagreements

```
Author: "I think approach X is better because performance"
Reviewer: "I prefer Y because readability"

Resolution: Discuss tradeoffs → Escalate to team lead if needed
             → Document decision → Move forward
```

## Metrics to Track

| Metric | What It Measures | Good Target |
|--------|-----------------|-------------|
| Review depth | Comments per PR | 3-10 substantive comments |
| Time to review | First response | < 4 hours |
| Time to merge | PR open duration | < 2 days |
| Rework rate | Revisions per PR | 1-3 revisions |
| Satisfaction | Developer survey | > 4/5 |

**Links**: [[Code Review Best Practices]] | [[Clean Code Principles]] | [[Git Pull Requests]] | [[Developer Workflow Automation]] | [[CI CD Pipelines]]
