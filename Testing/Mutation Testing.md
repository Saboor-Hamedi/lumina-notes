---
id: a1b2c3d4-1167-4000-8000-000000000167
title: Mutation Testing
language: markdown
tags: [testing, mutation-testing]
selection: null
isPinned: false
timestamp: 1781500001167
---

**Links**: [[Property-Based Testing]] | [[Unit Testing Guide]] | [[Test-Driven Development]] | [[Software Testing Pyramid]] | [[Integration Testing Patterns]] | [[Snapshot Testing]]


# Mutation Testing

Mutation testing evaluates test quality by introducing small changes (mutations) to code and checking whether tests catch them. It measures how well tests detect faults.

## How Mutation Testing Works

```
Original code:     if (x > 5) { return true; }

Mutation 1:        if (x >= 5) { return true; }   (changed > to >=)
Mutation 2:        if (false) { return true; }      (replaced condition)
Mutation 3:        if (x > 5) { return false; }     (negated return)

For each mutation:
  - Run all tests
  - Tests FAIL → mutation KILLED (good — tests caught it)
  - Tests PASS → mutation SURVIVED (bad — test gap found)
```

## Mutation Score

```
Mutation Score = Killed Mutations / Total Mutations

Score = 90% means 90% of mutations were caught by tests
Score < 80% suggests weak test coverage
```

## Mutation Operators

### Arithmetic Operators

```python
# Original
total = price + tax

# Mutations
total = price - tax        # + → -
total = price * tax        # + → *
total = price / tax        # + → /
total = price % tax        # + → %
total = price ** tax       # + → **
total = price              # Remove expression
```

### Comparison Operators

```python
# Original
if age >= 18:

# Mutations
if age > 18:               # >= → >
if age < 18:               # >= → <
if age <= 18:              # >= → <=
if age == 18:              # >= → ==
if age != 18:              # >= → !=
if True:                   # Condition → True
if False:                  # Condition → False
```

### Logical Operators

```python
# Original
if is_admin or is_moderator:

# Mutations
if is_admin and is_moderator:   # or → and
if not is_admin:                # Remove RHS
if not is_moderator:            # Remove LHS
if False:                       # Replace with False
```

### Return Values

```python
# Original
def is_valid(x):
    return x > 0

# Mutations
def is_valid(x):
    return False                 # Negate return
def is_valid(x):
    return True                  # Always true
def is_valid(x):
    return None                  # Return None
```

### Variable Mutations

```python
# Original
x = calculate_value()

# Mutations
x = None                        # Assign null
x = ""                          # Assign empty string
x = 0                           # Assign zero
x = {}                          # Assign empty collection
```

## Example in Practice

```python
import mutmut  # Python mutation testing tool

# Code to test
def calculate_discount(price, customer_type):
    if customer_type == "premium":
        return price * 0.8
    elif customer_type == "vip":
        return price * 0.7
    return price

# Tests
def test_calculate_discount():
    assert calculate_discount(100, "regular") == 100
    assert calculate_discount(100, "premium") == 80
    assert calculate_discount(100, "vip") == 70

# Mutation results might show:
#  - Line 3: price * 0.8 → price * 0.7 (SURVIVED!)
#    → Missing test: verify premium != vip discount
#  - Line 5: price * 0.7 → price * 0.8 (SURVIVED!)
#    → Same issue
```

## Mutation Testing Tools

| Language | Tool | Notes |
|----------|------|-------|
| Python | mutmut | Fast, AST-based |
| Python | mutpy | Original Python mutator |
| Java | PIT | Industry standard, fast |
| JavaScript | Stryker | Multi-language (JS, TS, C#, Scala) |
| Ruby | mutant | Full Ruby mutation testing |
| Go | go-mutesting | Go mutation testing |
| Rust | mutagen | Rust mutation testing |
| .NET | Stryker.NET | .NET mutation testing |
| Kotlin | Pitest + kotlin plugin | Kotlin mutation testing |

## Integration with CI

```yaml
# CI pipeline with mutation testing (Stryker)
jobs:
  test:
    steps:
      - run: npm ci
      - run: npm test                   # Regular tests first
      - run: npx stryker run            # Mutation tests
        env:
          STRYKER_DASHBOARD_API_KEY: ${{ secrets.STRYKER_KEY }}

      - name: Check mutation score
        run: |
          SCORE=$(jq '.mutationScore' stryker-report.json)
          if (( $(echo "$SCORE < 80" | bc -l) )); then
            echo "Mutation score $SCORE is below 80%"
            exit 1
          fi
```

## Limitations

| Limitation | Impact | Mitigation |
|------------|--------|------------|
| Slow execution | 10-100x slower than unit tests | Run only on changed code, use faster tools |
| Equivalent mutations | False positives (mutations that don't change behavior) | Manual review, equivalent detection heuristics |
| Large number of mutants | Millions of mutants for large codebases | Selective mutation, incremental analysis |
| Not suitable for all code | Integration tests, infrastructure code | Use only for unit-testable logic |
