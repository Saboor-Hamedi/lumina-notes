---
id: 57696d6e-b8d6-4593-82c1-10056933f6ca
title: Unit Testing Guide
language: markdown
tags: [testing, unit-testing]
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
      /  \         E2E Tests (few, slow, costly)
     /    \
    /------\
   /  Integration \
  /     Tests      \
 /------------------\
/   Unit Tests       \   (many, fast, cheap)
/_____________________\
```

Unit tests should make up the bulk of the test suite — they execute in milliseconds, require no external services, and pinpoint failures precisely.

## Arrange-Act-Assert (AAA) Pattern

AAA structures each test into three clear phases:

| Phase | Purpose | Example |
|-------|---------|---------|
| **Arrange** | Set up inputs, mocks, and preconditions | Create objects, initialize data, configure mocks |
| **Act** | Execute the behavior under test | Call the function or method |
| **Assert** | Verify the result matches expectations | Check return value, side effects, or exceptions |

```python
def test_withdraw_reduces_balance():
    # Arrange
    account = BankAccount(balance=100)
    
    # Act
    account.withdraw(30)
    
    # Assert
    assert account.balance == 70
    assert account.transactions == [-30]
```

## F.I.R.S.T. Principles

| Principle | Meaning | Why It Matters |
|-----------|---------|----------------|
| **F**ast | Tests run quickly (ms) | Slow tests discourage frequent execution |
| **I**solated | Tests don't share state | One test should never affect another's outcome |
| **R**epeatable | Same result every run | No dependency on time, network, or external state |
| **S**elf-validating | Pass/fail is automatic | No manual inspection of output files or logs |
| **T**imely | Written alongside (or before) production code | Delayed tests invite untested code and regressions |

## Mocking vs Stubbing vs Faking

| Technique | Purpose | Example |
|-----------|---------|---------|
| **Mock** | Records calls and verifies interactions | Assert that `email_service.send()` was called exactly once with specific args |
| **Stub** | Returns canned responses to drive code paths | `database.get_user(42)` always returns `{"name": "Alice"}` |
| **Fake** | Lightweight working implementation (not production) | In-memory database, fake file system |

```python
from unittest.mock import Mock, patch

def test_email_notification_sent():
    # Mock the email service
    email_mock = Mock()
    email_mock.send.return_value = True
    
    service = NotificationService(email_mock)
    service.notify_user("user@example.com", "Welcome!")
    
    # Verify interaction (not just return value)
    email_mock.send.assert_called_once_with(
        "user@example.com", 
        "Welcome!"
    )
```

## What to Test vs What Not to Test

### Test these
- Core business logic and calculations
- Boundary cases (empty, null, max, min, zero, negative)
- Error paths and exception handling
- Input validation and parsing
- State transitions and edge conditions

### Don't test these
- Framework internals (Django, Flask, Spring, etc.)
- Generated code (migrations, schemas, client SDKs)
- Trivial getters/setters (unless they contain logic)
- Third-party library behavior (assume they work)
- Configuration constants (test their effect, not the value)

## Test Coverage Guidelines

| Coverage Level | Target | Notes |
|----------------|--------|-------|
| Statement | >80% | Every line executed at least once |
| Branch | >75% | Both true/false paths in conditionals |
| Function | >90% | Every public function called |
| Line | >80% | Higher bar for mission-critical modules |

```bash
# Measure coverage with pytest-cov
pytest --cov=myapp --cov-report=term-missing
```

> Coverage is a **guide**, not a goal. 100% coverage doesn't guarantee bug-free code. Focus on meaningful assertions over line counting.

## Python Example (pytest)

```python
import pytest
from uuid import uuid4

def create_user(name, email):
    return {"id": str(uuid4()), "name": name, "email": email, "active": True}

def test_create_user_returns_valid_user():
    user = create_user("Alice", "alice@example.com")
    
    assert user["name"] == "Alice"
    assert user["email"] == "alice@example.com"
    assert user["active"] is True
    assert len(user["id"]) == 36  # UUID format

def test_divide_by_zero():
    with pytest.raises(ZeroDivisionError):
        divide(10, 0)

@pytest.mark.parametrize("a,b,expected", [
    (2, 3, 5),
    (-1, 1, 0),
    (0, 0, 0),
    (100, -100, 0),
])
def test_add_parametrized(a, b, expected):
    assert add(a, b) == expected
```

## Best Practices

- **One assertion per test** where possible (or logically grouped assertions)
- **Arrange-Act-Assert** pattern
- **Mock external dependencies** (DB, APIs, filesystem)
- **Test edge cases**: empty, null, boundary values, errors
- **Name tests descriptively**: `test_withdraw_insufficient_balance_raises_error`
- Keep tests deterministic — no random values without seeded generators
- Use test fixtures for shared setup (pytest fixtures, `@BeforeEach`)
- Prefer parameterized tests over looping in a single test case

**Links**: [[Software Testing Pyramid]] | [[Test-Driven Development]] | [[CI CD Pipelines]] | [[Clean Code Principles]] | [[Developer Workflow Automation]] | [[Software Design Principles]] | [[Code Architecture Patterns]] | [[Refactoring Techniques]]

**See also**: [[API Testing]], [[Integration Testing Patterns]], [[Git Hooks]]
