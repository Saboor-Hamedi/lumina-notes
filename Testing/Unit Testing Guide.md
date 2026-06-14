---
id: 57696d6e-b8d6-4593-82c1-10056933f6ca
title: Unit Testing Guide
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781229861605
---

**Links**: [[Software Testing Pyramid]] | [[Integration Testing Patterns]] | [[Test-Driven Development]] | [[API Testing]] | [[Mutation Testing]] | [[Property-Based Testing]]


# Unit Testing Guide

Unit testing verifies that individual units of source code work correctly. It is the first line of defense against bugs and enables confident refactoring.

## Testing Pyramid

```
       /\
      /  \         E2E Tests (few)
     /    \
    /------\
   /  Integration \
  /     Tests      \
 /------------------\
/   Unit Tests       \   (many, fast)
/_____________________\
```

## Best Practices

- **One assertion per test** where possible
- **Arrange-Act-Assert** pattern
- **Mock external dependencies** (DB, APIs, filesystem)
- **Test edge cases**: empty, null, boundary values, errors
- **Name tests descriptively**: `test_withdraw_insufficient_balance_raises_error`

## Python Example

```python
import pytest

def test_addition():
    result = add(2, 3)
    assert result == 5

def test_divide_by_zero():
    with pytest.raises(ZeroDivisionError):
        divide(10, 0)
```

**Links**: [[Software Testing Pyramid]] | [[Test-Driven Development]] | [[CI CD Pipelines]] | [[Clean Code Principles]] | [[Developer Workflow Automation]] | [[Software Design Principles]] | [[Code Architecture Patterns]] | [[Refactoring Techniques]]

**See also**: [[API Testing]], [[Integration Testing Patterns]], [[Git Hooks]]
