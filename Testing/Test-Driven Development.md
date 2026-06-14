---
id: a1b2c3d4-1205-4000-8000-000000000205
title: Test-Driven Development
language: markdown
tags: [testing, tdd]
selection: null
isPinned: false
timestamp: 1781500001205
---

**Links**: [[Unit Testing Guide]] | [[Integration Testing Patterns]] | [[Software Testing Pyramid]] | [[Mutation Testing]] | [[Property-Based Testing]] | [[API Testing]]


# Test-Driven Development (TDD)

TDD is a software development process where tests are written before the implementation. The cycle is Red-Green-Refactor.

## The TDD Cycle

```
RED:   Write a failing test (specify desired behavior)
GREEN: Write minimal code to make the test pass
REFACTOR: Improve code while keeping tests green

Loop for each new feature or behavior.

1. Write test → Run → FAIL (red)
2. Write code → Run → PASS (green)
3. Refactor → Run → STILL PASS
4. Goto 1 for next behavior
```

## Example: FizzBuzz

```python
# Step 1: RED — Write failing test
def test_fizzbuzz_returns_1_for_1():
    assert fizzbuzz(1) == "1"

def test_fizzbuzz_returns_2_for_2():
    assert fizzbuzz(2) == "2"

def test_fizzbuzz_returns_fizz_for_3():
    assert fizzbuzz(3) == "Fizz"

def test_fizzbuzz_returns_buzz_for_5():
    assert fizzbuzz(5) == "Buzz"

def test_fizzbuzz_returns_fizzbuzz_for_15():
    assert fizzbuzz(15) == "FizzBuzz"

# Step 2: GREEN — Write minimal code
def fizzbuzz(n):
    if n % 15 == 0:
        return "FizzBuzz"
    if n % 3 == 0:
        return "Fizz"
    if n % 5 == 0:
        return "Buzz"
    return str(n)

# Step 3: REFACTOR — Improve without changing behavior
# (In this case, the solution is already clean)
```

## Example: Shopping Cart

```python
# 1. RED — Test empty cart
def test_empty_cart_has_zero_items():
    cart = ShoppingCart()
    assert cart.total_items() == 0

def test_empty_cart_total_is_zero():
    cart = ShoppingCart()
    assert cart.total() == 0

# GREEN — Minimal implementation
class ShoppingCart:
    def __init__(self):
        self.items = []

    def total_items(self):
        return 0

    def total(self):
        return 0.0

# 2. RED — Test adding items
def test_add_single_item():
    cart = ShoppingCart()
    cart.add_item("apple", 1.0)
    assert cart.total_items() == 1
    assert cart.total() == 1.0

def test_add_multiple_items():
    cart = ShoppingCart()
    cart.add_item("apple", 1.0)
    cart.add_item("banana", 0.5)
    assert cart.total_items() == 2
    assert cart.total() == 1.5

# GREEN
class ShoppingCart:
    def __init__(self):
        self.items = []

    def add_item(self, name, price):
        self.items.append((name, price))

    def total_items(self):
        return len(self.items)

    def total(self):
        return sum(price for _, price in self.items)

# 3. RED — Test removing items
def test_remove_item():
    cart = ShoppingCart()
    cart.add_item("apple", 1.0)
    cart.add_item("banana", 0.5)
    cart.remove_item("apple")
    assert cart.total_items() == 1
    assert cart.total() == 0.5

# GREEN
def remove_item(self, name):
    self.items = [(n, p) for n, p in self.items if n != name]

# 4. REFACTOR
# (Extract domain logic if needed, add edge case handling)
```

## TDD Principles

| Principle | Description |
|-----------|-------------|
| Write test first | Forces you to think about interface before implementation |
| One assertion per test | Tests should verify one behavior |
| Test the behavior, not the implementation | Tests should pass after refactoring |
| Don't write code without a failing test | Avoids untested code |
| Write the simplest code to pass | YAGNI — don't add complexity prematurely |
| Red-Green-Refactor | Never skip the refactor step |

## Types of Tests in TDD

```python
# Acceptance tests (outer loop)
def test_complete_order_workflow():
    # Full user flow: create cart → add items → checkout → confirm

# Integration tests
def test_repository_saves_user():
    repo = UserRepository(db_session)
    user = repo.create("Alice", "alice@example.com")
    assert db_session.query(User).count() == 1

# Unit tests (inner TDD loop)
def test_user_full_name():
    user = User("Alice", "Smith")
    assert user.full_name == "Alice Smith"
```

## Mocking in TDD

```python
from unittest.mock import Mock, patch

def test_order_processor_sends_email():
    email_service = Mock()
    processor = OrderProcessor(email_service)

    processor.process_order(order_data)

    email_service.send_confirmation.assert_called_once_with(
        "customer@example.com", "order_123"
    )

def test_external_api_failure_handling():
    with patch("app.services.requests.post") as mock_post:
        mock_post.side_effect = ConnectionError("API unavailable")

        processor = OrderProcessor()
        result = processor.process_order(order_data)

        assert result.status == "failed"
        assert "API unavailable" in result.error
```

## Common Anti-Patterns

| Anti-Pattern | Issue | Fix |
|-------------|-------|-----|
| Testing implementation | Brittle tests | Test behavior, not internals |
| Too many mocks | Tests don't test real interaction | Integration tests for critical paths |
| Tests that don't fail | Suspicious — might be wrong | Verify test fails before implementation |
| Giant test methods | Unclear what's tested | One behavior per test |
| Shared mutable state | Tests interfere | Fresh fixtures per test |
| Testing trivial code | Too much overhead | Focus on logic, not getters/setters |

## TDD in CI

```yaml
# CI pipeline enforcing TDD
jobs:
  test:
    steps:
      - run: npm install
      - run: npm test           # Must pass
      - run: npm run coverage   # Minimum coverage check

  # Optional: mutation testing for TDD quality
  mutation:
    steps:
      - run: npx stryker run
        env:
          STRYKER_DASHBOARD_API_KEY: ${{ secrets.STRYKER_KEY }}
```
