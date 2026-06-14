---
id: a1b2c3d4-0004-4000-8000-000000000004
title: GraphQL API Design
language: markdown
tags: [web-dev, graphql, api-design]
selection: null
isPinned: false
timestamp: 1781360348577
---

**Links**: [[GraphQL]] | [[REST API Design]] | [[API Gateway Patterns]] | [[API Versioning]] | [[gRPC]] | [[HTTP Protocol]]


# GraphQL API Design

GraphQL is a query language for APIs that lets clients request exactly the data they need.

## Schema Definition

```graphql
type User {
  id: ID!
  name: String!
  email: String!
  posts: [Post!]!
}

type Post {
  id: ID!
  title: String!
  content: String!
  author: User!
}

type Query {
  user(id: ID!): User
  posts(limit: Int): [Post!]!
}
```

## Queries vs REST

| Aspect | REST | GraphQL |
|--------|------|---------|
| Data fetching | Multiple endpoints | Single endpoint |
| Over-fetching | Common | None |
| Under-fetching | Common (n+1) | None |
| Versioning | URL-based | Evolve schema |
| Caching | HTTP caching | Requires tools (Apollo) |

## Resolvers

```python
def resolve_user(root, info, id):
    return db.query("SELECT * FROM users WHERE id = %s", id)
```

**See also**: [[REST API Design]], [[HTTP Protocol]], [[SQL Query Optimization]]
