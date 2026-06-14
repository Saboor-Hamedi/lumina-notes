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
