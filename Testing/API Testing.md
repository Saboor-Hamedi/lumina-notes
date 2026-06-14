---
id: dev-066-0000-0000-0000-000000000055
title: API Testing
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781900000007
---
# API Testing

**Links**: [[REST API Design]] | [[Unit Testing Guide]] | [[HTTP Protocol]] | [[JSON Web Tokens]] | [[CI CD Pipelines]]

## What is API Testing?

API testing validates endpoints for correctness, performance, security, and reliability.

## Test Types

| Type | What It Tests |
|------|---------------|
| **Functional** | Returns correct response for valid/invalid input |
| **Integration** | Works correctly with dependencies |
| **Contract** | API matches its specification |
| **Security** | Auth, injection, rate limiting |
| **Performance** | Response time under load |

## Python (FastAPI + pytest)

```python
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

class TestUsersAPI:
    def test_create_user(self):
        response = client.post("/users", json={
            "name": "Alice",
            "email": "alice@example.com"
        })
        assert response.status_code == 201
        data = response.json()
        assert data["name"] == "Alice"
        assert "id" in data

    def test_create_user_invalid_email(self):
        response = client.post("/users", json={
            "name": "Invalid",
            "email": "not-an-email"
        })
        assert response.status_code == 422

    def test_auth_required(self):
        response = client.get("/users/me")
        assert response.status_code == 401
```

## What to Test

- **Happy path**: Valid request → correct response
- **Error cases**: Missing fields, invalid types, wrong values
- **Auth**: No token, expired token, insufficient permissions
- **Edge cases**: Empty results, pagination boundaries
- **Idempotency**: Same request multiple times

## Test Structure

```python
# Arrange
payload = {"name": "Alice", "email": "alice@example.com"}

# Act
response = client.post("/users", json=payload)

# Assert
assert response.status_code == 201
assert response.json()["name"] == "Alice"
```

## Postman / Newman

```bash
# Run Postman collection from CLI
npx newman run collection.json -e environment.json --reporters cli
```

**Next**: [[Load Testing]] — Performance under pressure