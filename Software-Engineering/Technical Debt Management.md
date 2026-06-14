---
id: a1b2c3d4-1079-4000-8000-000000000079
title: Technical Debt Management
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001079
---
# Technical Debt Management

Technical debt is the implied cost of future rework caused by choosing an easy or quick solution now over a better approach that would take longer.

## Types of Technical Debt

| Type | Description | Example |
|------|-------------|---------|
| Intentional | Deliberate shortcut (known tradeoff) | Ship now, refactor later |
| Unintentional | Accidental complexity (lack of knowledge) | Wrong abstraction, bad pattern |
| Bit rot | Code degrades without maintenance | Unused code, outdated dependencies |
| Design debt | Architecture lacks flexibility | Monolith that should be modular |
| Test debt | Missing or poor tests | Low coverage, flaky tests |
| Documentation debt | Outdated or missing docs | No README, stale API docs |
| Infrastructure debt | Outdated tooling | Manual deploys, old CI |

## Debt Quadrant (Fowler)

```
                Intentional              Unintentional
Reckless   "No time to design"      "What's MVC?"
           (ship and pray)           (don't know what we don't know)
Prudent    "Ship now, refactor"     "Now we understand"
           (we'll pay it back)       (learned from experience)
```

## Measuring Technical Debt

| Metric | How | Target |
|--------|-----|--------|
| Code churn | % of code changed frequently | < 20% |
| Complexity | Cyclomatic complexity per function | < 10 |
| Code coverage | % lines covered by tests | > 80% |
| Duplication | % duplicated code | < 5% |
| TODOs/FIXMEs | Count of pending tasks | < 50 |
| SonarQube rating | A-E rating | A or B |

```python
# Simple debt estimation
def estimate_debt(file_path):
    with open(file_path) as f:
        source = f.read()

    debt_minutes = 0

    debt_minutes += 5 * source.count("TODO")      # 5 min per TODO
    debt_minutes += 10 * source.count("FIXME")     # 10 min per FIXME
    debt_minutes += 15 * source.count("HACK")      # 15 min per HACK
    debt_minutes += 30 * source.count("XXX")       # 30 min per XXX

    # More sophisticated: cyclomatic complexity > 10
    # Lack of tests, outdated comments, etc.

    return debt_minutes
```

## Managing Technical Debt

### The 20% Rule

Dedicate 20% of each sprint to paying down debt:

```python
sprint_capacity = team_velocity  # story points
debt_capacity = sprint_capacity * 0.2  # reserve for debt

# Track debt items alongside features
sprint_backlog = [
    Feature("User login"),           # 8 pts
    Feature("Password reset"),       # 5 pts
    Debt("Refactor auth module"),    # 3 pts  ← 20% rule
    Debt("Add tests for profile"),   # 2 pts  ← 20% rule
]
```

### Boy Scout Rule

"Leave the code cleaner than you found it."

When touching a file, make it slightly better: rename a variable, add a test, extract a function.

### Debt Backlog

Treat debt like features — estimate, prioritize, schedule:

| Priority | Debt Item | Effort | Impact |
|----------|-----------|--------|--------|
| P0 | Security vulnerability | 2 days | Critical |
| P1 | Slow CI pipeline | 3 days | High (blocks devs) |
| P2 | Untested payment module | 5 days | High (risk) |
| P3 | Outdated documentation | 2 days | Low |

## When to Pay vs When to Carry

| Pay the debt | Carry the debt |
|-------------|----------------|
| Affects development velocity | One-time shortcut with low risk |
| High risk (security, correctness) | Easy to fix later |
| Touched by many features | Obsolete code being replaced soon |
| Team doesn't understand it | Code with clear API boundaries |

## Prevention

| Practice | Prevents |
|----------|----------|
| Code reviews | Intentional shortcuts unchecked |
| Architecture Decision Records (ADRs) | Repeated mistakes |
| Automated quality gates | Regressions |
| Knowledge sharing | Skill debt |
| Regular refactoring cadence | Accumulated complexity |

## Tools

| Tool | Use |
|------|-----|
| SonarQube | Code quality dashboard |
| CodeClimate | Maintainability ratings |
| NDepend | .NET static analysis |
| Sourcery | Python refactoring suggestions |
| Dependabot/Renovate | Dependency updates |
| Deprecation scanners | Dead code detection |

**Links**: [[Clean Code Principles]] | [[Refactoring Techniques]] | [[Code Review Process]] | [[Software Design Principles]] | [[Code Architecture Patterns]]
