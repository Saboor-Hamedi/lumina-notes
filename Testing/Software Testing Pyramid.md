---
id: a1b2c3d4-1004-4000-8000-000000000004
title: Software Testing Pyramid
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001004
---
# Software Testing Pyramid

The testing pyramid guides teams to invest in many fast, isolated unit tests, fewer integration tests, and even fewer end-to-end tests.

## Test Layers

```
        /\        E2E Tests (slow, brittle, high confidence)
       /  \
      /    \       Integration Tests (medium speed)
     /______\
    /________\     Unit Tests (fast, many, isolated)
```

### Unit Tests
- Test individual functions/classes in isolation
- Mock external dependencies
- Run in milliseconds
- Coverage target: 70-80%

### Integration Tests
- Test interactions between components
- Use real databases, filesystems, or test containers
- Run in seconds
- Cover API endpoints, data access, external services

### End-to-End Tests
- Test full user workflows through the UI
- Use browser automation (Playwright, Cypress, Selenium)
- Run in minutes
- Cover critical user journeys only

## Testing Methodologies

| Approach | Focus | Who Writes |
|----------|-------|------------|
| TDD | Write tests before code | Developers |
| BDD | Describe behavior in natural language | Dev + QA + Product |
| ATDD | Define acceptance criteria as tests | Team |

## TDD Cycle (Red-Green-Refactor)

1. Write a failing test (Red)
2. Write minimal code to pass (Green)
3. Refactor while keeping tests green

## Mocking vs Stubbing

| Double | Behavior |
|--------|----------|
| Dummy | Passed but never used |
| Stub | Returns fixed values |
| Spy | Records how it was called |
| Mock | Pre-programmed with expectations |
| Fake | Working lightweight implementation |

## Code Coverage Types

- **Line**: Which lines were executed
- **Branch**: Which condition branches were taken
- **Function**: Which functions were called
- **Mutation**: Tests detect changes in code

**Links**: [[Unit Testing Guide]] | [[API Testing]] | [[CI CD Pipelines]] | [[Error Handling Patterns]] | [[Load Testing]]
