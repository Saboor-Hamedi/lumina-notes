---
id: a1b2c3d4-1109-4000-8000-000000000109
title: API Gateway Patterns
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001109
---
# API Gateway Patterns

An API gateway is a reverse proxy that sits between clients and backend services, handling cross-cutting concerns like auth, routing, throttling, and aggregation.

## Gateway Responsibilities

| Concern | How Gateway Handles It |
|---------|----------------------|
| Authentication | Validate JWT, API keys, OAuth tokens |
| Rate limiting | Per-client/per-endpoint throttling |
| Routing | Path-based to appropriate backend |
| Load balancing | Distribute across service instances |
| Caching | Cache frequent responses |
| Transformation | Protocol conversion (REST ↔ gRPC) |
| Aggregation | Combine multiple backend responses |
| Monitoring | Metrics, logging, distributed tracing |
| Circuit breaking | Stop routing to unhealthy services |

## Architecture Patterns

### Single Gateway (Monolithic)

```
Client → [API Gateway] → Service A
                         Service B
                         Service C
```

**Pros**: Simple, single point of control.
**Cons**: Single point of failure, scaling bottleneck.

### BFF (Backend for Frontend)

```
Web App → [Web Gateway] → Auth, Content
Mobile → [Mobile Gateway] → Auth, Content, Push
IoT → [IoT Gateway] → Device, Telemetry
```

Each client type gets a tailored gateway. Google/Meta approach.

### Sidecar Gateway

```
Service A ←→ Sidecar (Envoy) ←→ Service B
                    ↓
              Control Plane
```

Service mesh pattern — gateway logic attached to each service.

## Popular Gateways

| Gateway | Type | Best For |
|---------|------|----------|
| Kong | Open source | Plugin ecosystem, enterprise features |
| Tyk | Open source | API management, developer portal |
| NGINX + Lua | Proxy + scripting | Custom logic, high performance |
| AWS API Gateway | Managed | Serverless, AWS ecosystem |
| Azure API Management | Managed | Azure ecosystem |
| Envoy | High-performance proxy | Service mesh, gRPC, dynamic config |
| KrakenD | Open source | Performance-focused, no plugins |

## Configuration Example (Kong)

```yaml
# Declarative config
_format_version: "3.0"
services:
  - name: users
    url: http://users-service:8080
    routes:
      - name: users-route
        paths:
          - /api/users
        methods:
          - GET
          - POST
    plugins:
      - name: rate-limiting
        config:
          minute: 100
          policy: local

  - name: orders
    url: http://orders-service:8080
    routes:
      - name: orders-route
        paths:
          - /api/orders
        methods:
          - GET
          - POST

plugins:
  - name: jwt
    config:
      secret: ${JWT_SECRET}
      claims_to_verify:
        - exp
```

## Request/Response Transformation

```yaml
# Kong plugin: request-transformer
plugins:
  - name: request-transformer
    config:
      add:
        headers:
          X-Gateway-Version: "1.0"
          X-Request-ID: "$(uuid)"
      remove:
        headers:
          - X-Internal-Token
```

## Response Aggregation

```python
# BFF aggregation endpoint
@gateway.get("/api/dashboard")
async def get_dashboard(user_id: str):
    # Fan-out requests in parallel
    user = asyncio.create_task(fetch_user(user_id))
    orders = asyncio.create_task(fetch_orders(user_id))
    recommendations = asyncio.create_task(fetch_recommendations(user_id))

    # Aggregate
    return {
        "user": await user,
        "orders": await orders,
        "recommendations": await recommendations,
    }
```

## Backend as a Gateway (AWS API Gateway)

```yaml
Resources:
  ApiGateway:
    Type: AWS::ApiGateway::RestApi
    Properties:
      Name: my-api

  UsersResource:
    Type: AWS::ApiGateway::Resource
    Properties:
      RestApiId: !Ref ApiGateway
      ParentId: !GetAtt ApiGateway.RootResourceId
      PathPart: users

  UsersMethod:
    Type: AWS::ApiGateway::Method
    Properties:
      RestApiId: !Ref ApiGateway
      ResourceId: !Ref UsersResource
      HttpMethod: GET
      AuthorizationType: JWT
      AuthorizerId: !Ref Authorizer
      Integration:
        Type: HTTP_PROXY
        Uri: http://users-service:8080/users
```

**Links**: [[Microservices Architecture]] | [[REST API Design]] | [[Envoy Proxy]] | [[Kong Gateway]] | [[API Security]]
