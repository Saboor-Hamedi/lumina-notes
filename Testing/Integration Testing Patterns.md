---
id: a1b2c3d4-1130-4000-8000-000000000130
title: Integration Testing Patterns
language: markdown
tags: [testing, integration-testing]
selection: null
isPinned: false
timestamp: 1781500001130
---

**Links**: [[Software Testing Pyramid]] | [[Unit Testing Guide]] | [[API Testing]] | [[Test-Driven Development]] | [[Snapshot Testing]] | [[Performance Testing]]


# Integration Testing Patterns

Integration testing verifies that multiple components work together correctly — testing real interactions between modules, databases, APIs, and external services.

## Integration vs Unit vs E2E

| Aspect | Unit | Integration | E2E |
|--------|------|-------------|-----|
| Scope | Single function/class | Component interactions | Full user workflow |
| Speed | Milliseconds | Seconds | Minutes |
| Dependencies | All mocked | Real (or test doubles) | Real |
| Confidence | Low (isolated) | Medium | High |
| Maintenance | Low | Medium | High |
| Flakiness | Low | Medium | High |

## Database Integration

```python
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

@pytest.fixture
def db_session():
    # Use test database (or in-memory SQLite)
    engine = create_engine("sqlite:///:memory:")
    Base.metadata.create_all(engine)
    session = sessionmaker(bind=engine)()
    yield session
    session.close()

def test_create_user(db_session):
    repo = UserRepository(db_session)
    user = repo.create(name="Alice", email="alice@example.com")

    assert user.id is not None
    assert user.name == "Alice"

    # Verify persistence
    saved = db_session.query(User).filter_by(email="alice@example.com").first()
    assert saved is not None
```

## API Integration

```python
import pytest
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

@pytest.fixture
def auth_token():
    response = client.post("/auth/login", json={
        "username": "testuser",
        "password": "testpass"
    })
    return response.json()["access_token"]

def test_create_order(auth_token):
    # Test full request/response cycle
    response = client.post(
        "/api/orders",
        headers={"Authorization": f"Bearer {auth_token}"},
        json={
            "product_id": "prod_123",
            "quantity": 2,
            "shipping_address": "123 Main St"
        }
    )

    assert response.status_code == 201
    data = response.json()
    assert data["product_id"] == "prod_123"
    assert data["status"] == "pending"

    # Verify downstream effect
    response = client.get(f"/api/orders/{data['id']}")
    assert response.status_code == 200
```

## Test Containers (Docker)

```java
// Java with Testcontainers
@Testcontainers
class OrderRepositoryTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16")
            .withDatabaseName("testdb")
            .withUsername("test")
            .withPassword("test");

    @Test
    void shouldPersistAndRetrieveOrder() {
        // Real PostgreSQL running in Docker
        Order saved = repository.save(new Order("user_1", 99.99));
        Order retrieved = repository.findById(saved.getId());
        assertEquals(saved.getAmount(), retrieved.getAmount());
    }
}
```

```python
# Python with testcontainers
from testcontainers.postgres import PostgresContainer

def test_with_real_postgres():
    with PostgresContainer("postgres:16") as postgres:
        engine = create_engine(postgres.get_connection_url())
        # Run migrations, test against real PostgreSQL
```

## Contract Testing

Verify that two services agree on the API contract:

```python
# Provider-side (API server)
from pactman import Provider

@pytest.fixture
def provider():
    return Provider("UserService")

def test_user_contract(provider):
    # Defined consumer expectations
    (provider
        .given("user 123 exists")
        .upon_receiving("a request for user 123")
        .method("GET")
        .path("/api/users/123")
        .will_respond_with(200, body={
            "id": "123",
            "name": "Alice",
            "email": "alice@example.com"
        }))

    # Verify provider meets expectations
    with provider:
        provider.verify()
```

## HTTP Mocking Patterns

```python
import responses

@responses.activate
def test_external_api_integration():
    # Mock external service
    responses.add(
        responses.GET,
        "https://api.external.com/v1/data",
        json={"status": "ok", "data": [1, 2, 3]},
        status=200
    )

    # Test our service that calls external API
    result = my_service.fetch_and_process()
    assert result.processed_count == 3
```

## WireMock (Java)

```java
@SpringBootTest
@WireMockTest(httpPort = 8089)
class OrderServiceTest {

    @Test
    void shouldCallPaymentGateway() {
        // Define expected interaction
        stubFor(post(urlEqualTo("/payments"))
                .withRequestBody(containing("99.99"))
                .willReturn(aResponse()
                        .withStatus(200)
                        .withHeader("Content-Type", "application/json")
                        .withBody("{\"status\": \"success\"}")));

        // Run test
        orderService.placeOrder(new Order(99.99));

        // Verify interaction happened
        verify(postRequestedFor(urlEqualTo("/payments"))
                .withRequestBody(containing("99.99")));
    }
}
```

## Async Integration Testing

```python
@pytest.mark.asyncio
async def test_message_queue_integration():
    # Publish message
    await publisher.publish("order.created", {"order_id": "123"})

    # Wait for consumer to process
    await asyncio.sleep(0.5)  # or: await wait_for_processing()

    # Verify side effect
    order = await db.find_order("123")
    assert order.status == "processed"
```

## Integration Test Checklist

- [ ] Database: In-memory or test container, not production
- [ ] External services: WireMock/testcontainers, not real
- [ ] State isolation: Clean state between tests
- [ ] Transactional: Roll back after each test
- [ ] Idempotent: Run multiple times, same result
- [ ] Mock boundaries: Mock at external boundary, not internal

**Links**: [[Unit Testing Guide]] | [[Software Testing Pyramid]] | [[API Testing]] | [[Test Doubles]] | [[Continuous Integration]]
