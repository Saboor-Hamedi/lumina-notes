---
id: 2630e31a-7adf-4701-a131-f2d48ae9c955
title: REST API Design
language: markdown
tags: [devops, rest, api-design]
selection: null
isPinned: false
timestamp: 1781226370013
---

**Links**: [[CI CD Pipelines]] | [[Docker Containers]] | [[Kubernetes Basics]] | [[API Gateway Patterns]] | [[gRPC]]


# REST API Design

REST (Representational State Transfer) is an architectural style for designing networked applications. It uses HTTP methods to perform CRUD operations on resources.

## Resource Naming

| Resource | Endpoint |
|----------|----------|
| Collection | `GET /users` |
| Single item | `GET /users/{id}` |
| Create | `POST /users` |
| Update | `PUT /users/{id}` |
| Partial update | `PATCH /users/{id}` |
| Delete | `DELETE /users/{id}` |

## HTTP Methods — Idempotency & Safety

| Method | Idempotent | Safe | Use Case |
|--------|------------|------|----------|
| GET | Yes | Yes | Retrieve resource |
| POST | No | No | Create resource |
| PUT | Yes | No | Full replacement |
| PATCH | No | No | Partial update |
| DELETE | Yes | No | Remove resource |
| HEAD | Yes | Yes | Headers only |
| OPTIONS | Yes | Yes | Discover methods |

> **Safe** = no side effects. **Idempotent** = repeated identical requests produce the same server state.

## HTTP Status Code Categories

| Code Range | Category | Common Codes |
|------------|----------|--------------|
| 1xx | Informational | 100 Continue, 101 Switching Protocols |
| 2xx | Success | 200 OK, 201 Created, 204 No Content |
| 3xx | Redirection | 301 Moved Permanently, 304 Not Modified |
| 4xx | Client Error | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 409 Conflict, 422 Unprocessable Entity, 429 Too Many Requests |
| 5xx | Server Error | 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable |

## Request & Response Format

```json
// POST /api/v1/users
// Request body
{
  "name": "Alice",
  "email": "alice@example.com",
  "role": "developer"
}

// Response — 201 Created
{
  "id": "a1b2c3d4",
  "name": "Alice",
  "email": "alice@example.com",
  "role": "developer",
  "createdAt": "2026-06-14T10:30:00Z",
  "_links": {
    "self": { "href": "/users/a1b2c3d4" },
    "orders": { "href": "/users/a1b2c3d4/orders" }
  }
}
```

Use snake_case or camelCase consistently. Prefer ISO 8601 for all date/time fields. Use UUIDs for resource identifiers.

## Pagination, Filtering & Sorting

```text
GET /users?page=2&limit=20&sort=createdAt:desc&filter=role=admin
```

- **Cursor-based**: `?cursor=abc123&limit=20` — stable for real-time data; new inserts don't shift pages.
- **Page/limit**: `?page=2&limit=20` — simple but off-by-one errors on concurrent inserts.
- **Filtering**: `?role=admin&status=active` — exact match or range (`age__gte=18`).
- **Sorting**: `?sort=name:asc,createdAt:desc` — multiple sort keys with direction.

Paginated response envelope:

```json
{
  "data": [ ... ],
  "pagination": {
    "page": 2,
    "limit": 20,
    "total": 143,
    "totalPages": 8
  }
}
```

## HATEOAS (Hypermedia as the Engine of Application State)

REST responses include links that clients follow dynamically, decoupling the client from hardcoded URL paths:

```json
{
  "orderId": "ord-001",
  "status": "shipped",
  "_links": {
    "self": { "href": "/orders/ord-001" },
    "cancel": { "href": "/orders/ord-001/cancel", "method": "POST" },
    "customer": { "href": "/users/cust-42" }
  }
}
```

A client that understands the `cancel` link relation can navigate without knowing the URL structure in advance.

## Standard Error Response

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email is already in use.",
    "details": [
      { "field": "email", "reason": "duplicate", "value": "alice@example.com" }
    ],
    "requestId": "req-abc-123",
    "timestamp": "2026-06-14T10:30:00Z"
  }
}
```

Always include a correlation/request ID. Use machine-readable error codes alongside human-readable messages so clients can handle errors programmatically.

## Best Practices

- Use nouns, not verbs (`/users` not `/getUsers`)
- Use plural nouns for collections
- Nest related resources (`/users/{id}/orders`)
- Use consistent error responses with proper HTTP status codes
- Version your API (`/v1/users`)
- Support pagination (`?page=1&limit=20`)
- Use query params for filtering and sorting

**Links**: [[HTTP Protocol]] | [[API Gateway Patterns]] | [[gRPC]] | [[GraphQL API Design]] | [[API Versioning]] | [[API Documentation with OpenAPI]] | [[Software Design Principles]] | [[Code Architecture Patterns]] | [[API Security]]

**See also**: [[Microservices Architecture]], [[OAuth and Authentication Protocols]], [[Web Development Fundamentals]]
