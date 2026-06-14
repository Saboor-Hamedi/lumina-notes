---
id: a1b2c3d4-0017-4000-8000-000000000017
title: API Versioning
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781400000017
---

**Links**: [[REST API Design]] | [[API Gateway Patterns]] | [[GraphQL API Design]] | [[HTTP Protocol]] | [[API Documentation with OpenAPI]] | [[Web Development Fundamentals]]


# API Versioning

API versioning allows you to evolve your API without breaking existing clients.

## Strategies

### URI Versioning

```
GET /v1/users
GET /v2/users
```

Simple but clutters URIs.

### Header Versioning

```
GET /users
Accept: application/vnd.myapp.v1+json
```

Cleaner URIs but harder to test in browser.

### Query Parameter

```
GET /users?version=1
```

Easy to implement but pollutes query strings.

## When to Version

- Breaking schema changes (renaming/removing fields)
- Changed behavior or semantics
- New required parameters
- Removed endpoints

## Best Practices

- Default to latest version for unversioned requests
- Deprecate old versions with clear sunset dates
- Support at most 2-3 versions simultaneously
- Communicate breaking changes via changelog

**See also**: [[REST API Design]], [[HTTP Protocol]], [[GraphQL API Design]]