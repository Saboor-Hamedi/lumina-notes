---
id: new-006-0000-0000-0000-000000000006
title: API Documentation
language: markdown
tags: [web-dev, api-docs]
selection: null
isPinned: false
timestamp: 1781600000006
---
# API Documentation

**Links**: [[REST API Design]] | [[HTTP Protocol]] | [[GraphQL API Design]] | [[API Versioning]] | [[Programming Resources]]

## Why Document APIs?

Good documentation is the interface between your API and its consumers. It reduces support burden, speeds integration, and prevents misuse.

## OpenAPI / Swagger

The industry standard for REST API documentation — a machine-readable specification that can generate docs, clients, and tests.

```yaml
# openapi.yaml
openapi: 3.1.0
info:
  title: User API
  version: 1.0.0
  description: API for managing users
paths:
  /users:
    get:
      summary: List all users
      parameters:
        - name: page
          in: query
          schema:
            type: integer
            default: 1
      responses:
        '200':
          description: A list of users
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'
    post:
      summary: Create a new user
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUser'
components:
  schemas:
    User:
      type: object
      properties:
        id: { type: integer }
        name: { type: string }
        email: { type: string }
```

## Python Example (FastAPI + OpenAPI)

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI(title="User API", version="1.0.0")

class User(BaseModel):
    name: str
    email: str

@app.get("/users", summary="List all users")
async def list_users(page: int = 1):
    """Returns a paginated list of users."""
    return {"users": [], "page": page}
```

FastAPI auto-generates OpenAPI docs at `/docs` (Swagger UI) and `/redoc`.

## Doc Generation Tools

| Tool | For |
|------|-----|
| Swagger UI | Interactive API explorer |
| Redoc | Clean, readable docs |
| Stoplight | Full API design and docs |
| Postman | API testing and documentation |

**Next**: [[Data Serialization]] — JSON, XML, Protobuf