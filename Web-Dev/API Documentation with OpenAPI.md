---
id: a1b2c3d4-1186-4000-8000-000000000186
title: API Documentation with OpenAPI
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001186
---

**Links**: [[REST API Design]] | [[API Gateway Patterns]] | [[API Versioning]] | [[GraphQL API Design]] | [[HTTP Protocol]] | [[gRPC]]


# API Documentation with OpenAPI

OpenAPI (formerly Swagger) is a specification for describing REST APIs. It generates documentation, client SDKs, and server stubs.

## Document Structure

```yaml
openapi: "3.0.3"
info:
  title: "Orders API"
  version: "1.2.0"
  contact:
    name: "API Support"
    email: "api@example.com"

servers:
  - url: https://api.example.com/v1
    description: Production

paths:
  /orders:
    get:
      summary: List orders
      operationId: listOrders
      parameters:
        - name: status
          in: query
          schema:
            type: string
            enum: [pending, confirmed, shipped]
        - name: page
          in: query
          schema:
            type: integer
            default: 1
      responses:
        "200":
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/OrderList"

    post:
      summary: Create order
      operationId: createOrder
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/CreateOrderRequest"
      responses:
        "201":
          description: Created
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Order"

components:
  schemas:
    Order:
      type: object
      properties:
        id:
          type: string
        customer_id:
          type: string
        total:
          type: number
          format: float
        status:
          type: string

    CreateOrderRequest:
      type: object
      required: [customer_id, items]
      properties:
        customer_id:
          type: string
        items:
          type: array
          items:
            $ref: "#/components/schemas/OrderItem"

    OrderItem:
      type: object
      properties:
        product_id:
          type: string
        quantity:
          type: integer
        price:
          type: number

    Error:
      type: object
      properties:
        code:
          type: string
        message:
          type: string

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

security:
  - bearerAuth: []
```

## Code-First vs Design-First

| Aspect | Code-First | Design-First |
|--------|------------|-------------|
| Workflow | Annotate code → Generate spec | Write spec → Generate code |
| Control | Code drives schema | Schema drives code |
| Speed | Faster | Slower but consistent |
| Collaboration | Backend-focused | Cross-team review |

```python
# Code-First with FastAPI
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI(title="Orders API", version="1.2.0")

class Order(BaseModel):
    id: str
    customer_id: str
    total: float
    status: str

@app.get("/orders", response_model=list[Order])
async def list_orders(status: str = None, page: int = 1, limit: int = 20):
    """List all orders with optional filtering."""
    pass

# Auto-generated at /docs or /openapi.json
```

## Rendering Documentation

```bash
# Swagger UI
docker run -p 80:8080 -e SWAGGER_JSON=/app/openapi.yaml -v $(pwd)/openapi.yaml:/app/openapi.yaml swaggerapi/swagger-ui

# Redoc
npx redoc-cli serve openapi.yaml
npx redoc-cli bundle openapi.yaml -o docs/api.html

# Validate
npx @redocly/cli lint openapi.yaml
npx swagger-cli validate openapi.yaml
```

## Versioning Strategies

| Method | Example |
|--------|---------|
| URL path | /v1/orders, /v2/orders |
| Header | Accept-Version: v2 |
| Content type | application/vnd.example.v2+json |

## Best Practices

| Practice | Why |
|----------|-----|
| Include examples per field | Developer understanding |
| Document error responses | Reduces integration time |
| Document auth first | First question developers ask |
| Validate spec in CI | Prevents broken docs |
| Keep spec in version control | Track changes, review in PR |
