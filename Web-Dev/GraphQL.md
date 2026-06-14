---
id: a1b2c3d4-1131-4000-8000-000000000131
title: GraphQL
language: markdown
tags: [web-dev, graphql, api]
selection: null
isPinned: false
timestamp: 1781500001131
---

**Links**: [[GraphQL API Design]] | [[REST API Design]] | [[API Gateway Patterns]] | [[WebSocket Deep Dive]] | [[gRPC]] | [[State Management Patterns]]


# GraphQL

GraphQL is a query language and runtime for APIs. Clients request exactly the data they need, nothing more and nothing less.

## Core Concepts

```
REST:   /users/123            → fixed response
        /users/123/posts      → another endpoint

GraphQL: POST /graphql        → client specifies shape
         query { user(id: 123) { name email posts { title } } }
```

## Schema Definition Language (SDL)

```graphql
type User {
    id: ID!
    name: String!
    email: String!
    posts: [Post!]!
    createdAt: DateTime!
}

type Post {
    id: ID!
    title: String!
    content: String!
    author: User!
    comments: [Comment!]!
}

type Query {
    user(id: ID!): User
    users(page: Int, limit: Int): [User!]!
    searchPosts(query: String!): [Post!]!
}

type Mutation {
    createUser(input: CreateUserInput!): User!
    updatePost(id: ID!, input: UpdatePostInput!): Post!
    deletePost(id: ID!): Boolean!
}

input CreateUserInput {
    name: String!
    email: String!
}
```

## Resolvers

```javascript
const resolvers = {
    Query: {
        user: async (_, { id }, { db }) => {
            return db.users.findById(id);
        },
        users: async (_, { page, limit }, { db }) => {
            return db.users.findAll({ skip: page, limit });
        }
    },
    User: {
        posts: async (parent, _, { db }) => {
            return db.posts.findByAuthor(parent.id);
        }
    },
    Mutation: {
        createUser: async (_, { input }, { db }) => {
            return db.users.create(input);
        }
    }
};
```

## N+1 Problem and DataLoader

```javascript
// N+1: For N users, makes N+1 queries (1 for users, N for posts)
const resolvers = {
    User: {
        posts: (user) => db.posts.findByAuthor(user.id) // N queries!
    }
};

// Solution: DataLoader — batches and caches
const DataLoader = require("dataloader");

const postLoader = new DataLoader(async (userIds) => {
    const posts = await db.posts.findByAuthorIds(userIds);
    return userIds.map(id => posts.filter(p => p.authorId === id));
});

const resolvers = {
    User: {
        posts: (user, _, { loaders }) => loaders.postLoader.load(user.id)
    }
};
// Now: 2 queries total (1 for users, 1 batched for posts)
```

## Client Example

```graphql
# Query
query GetUserWithPosts($userId: ID!) {
    user(id: $userId) {
        name
        email
        posts {
            title
            createdAt
        }
    }
}

# Variables
{ "userId": "123" }

# Response (only requested fields)
{
    "data": {
        "user": {
            "name": "Alice",
            "email": "alice@example.com",
            "posts": [
                { "title": "Hello World", "createdAt": "2026-06-13T10:00:00Z" }
            ]
        }
    }
}
```

## GraphQL vs REST

| Aspect | REST | GraphQL |
|--------|------|---------|
| Data fetching | Fixed responses | Client-specified |
| Over-fetching | Common | Eliminated |
| Under-fetching | Common (multiple endpoints) | Eliminated |
| Versioning | /v1/, /v2/ | Evolve schema |
| Caching | HTTP caching (URL-based) | Complex (persisted queries) |
| File upload | multipart/form-data | More complex |
| Tooling | curl, Postman | GraphiQL, Apollo DevTools |

## Subscriptions (Real-Time)

```graphql
type Subscription {
    postCreated: Post!
    commentAdded(postId: ID!): Comment!
}

# Server
const { PubSub } = require("graphql-subscriptions");
const pubsub = new PubSub();

const resolvers = {
    Subscription: {
        commentAdded: {
            subscribe: (_, { postId }) => pubsub.asyncIterator([`COMMENT_ADDED_${postId}`])
        }
    }
};
```

## Security Concerns

| Concern | Mitigation |
|---------|------------|
| Deep query nesting | Max depth validation |
| Query complexity | Cost analysis, rate limit by cost |
| Batched queries | Rate limiting per IP/token |
| Introspection | Disable in production |
| Field suggestions | Disable in production |

```javascript
// Depth limiting
app.use("/graphql", graphqlHTTP({
    schema,
    validationRules: [depthLimit(5)]
}));

// Query complexity
app.use("/graphql", graphqlHTTP({
    schema,
    validationRules: [costAnalysis({ maxCost: 1000 })]
}));
```

**Links**: [[API Security]] | [[REST API Design]] | [[Web Development Fundamentals]] | [[Web Performance Optimization]] | [[API Gateway Patterns]]
