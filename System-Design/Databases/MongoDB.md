---
id: a1b2c3d4-1111-4000-8000-000000000111
title: MongoDB
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001111
---

**Links**: [[MongoDB Deep Dive]] | [[Database Engines Compared]] | [[SQL vs NoSQL Databases]] | [[Cassandra]] | [[Database Sharding]] | [[Database Replication Strategies]]


# MongoDB

MongoDB is a document-oriented NoSQL database that stores data in flexible, JSON-like documents with a dynamic schema.

## Document Model

```json
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),
  "name": "Alice",
  "email": "alice@example.com",
  "address": {
    "city": "New York",
    "zip": "10001"
  },
  "hobbies": ["reading", "hiking"],
  "created_at": ISODate("2026-01-15T10:00:00Z")
}
```

| Feature | Document DB | Relational DB |
|---------|------------|---------------|
| Schema | Flexible, per-document | Fixed, per-table |
| Relationships | Embedded or referenced | JOINs across tables |
| Hierarchical data | Natural (nested documents) | Requires multiple tables |
| Atomic operations | Per-document | Multi-row transactions |

## CRUD Operations

```javascript
// Create
db.users.insertOne({ name: "Alice", email: "alice@example.com", age: 30 })
db.users.insertMany([{ name: "Bob" }, { name: "Carol" }])

// Read
db.users.find({ age: { $gt: 25 } })
db.users.findOne({ email: "alice@example.com" })
db.users.find({}, { name: 1, email: 1 })  // projection

// Update
db.users.updateOne({ _id: ObjectId("...") }, { $set: { age: 31 } })
db.users.updateMany({ age: { $lt: 18 } }, { $set: { status: "minor" } })

// Delete
db.users.deleteOne({ _id: ObjectId("...") })
db.users.deleteMany({ status: "inactive" })
```

## Indexing

| Index Type | Use Case |
|-----------|----------|
| Single field | Simple equality lookups |
| Compound | Multi-field queries (sort + filter) |
| Text | Full-text search |
| Geospatial | Location-based queries |
| TTL | Auto-expire documents |
| Hashed | Shard key distribution |

```javascript
db.users.createIndex({ email: 1 }, { unique: true })
db.orders.createIndex({ userId: 1, createdAt: -1 })
db.posts.createIndex({ content: "text", title: "text" })
db.sessions.createIndex({ createdAt: 1 }, { expireAfterSeconds: 86400 })
```

## Aggregation Pipeline

```javascript
db.orders.aggregate([
  { $match: { status: "completed" } },
  { $group: { _id: "$userId", total: { $sum: "$amount" }, count: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 10 },
  { $lookup: {
      from: "users",
      localField: "_id",
      foreignField: "_id",
      as: "user"
  }},
  { $unwind: "$user" },
  { $project: { "user.name": 1, total: 1, count: 1 } }
])
```

## Replication

```
Primary → Secondary (async)
       → Secondary (async)
       → Arbiter (votes only, no data)
```

- Automatic failover (election among secondaries)
- Read from secondaries (eventual consistency)
- Write concern: { w: "majority" } for durability

## Sharding

```
mongos (router)
    │
    ├── shard1 (replica set)
    ├── shard2 (replica set)
    └── shard3 (replica set)
```

- **Shard key**: Decides data distribution
- **Chunks**: Contiguous ranges of shard key values
- **Balancer**: Migrates chunks to balance shards

## When to Use MongoDB

| Good Fit | Poor Fit |
|----------|----------|
| Rapid prototyping | Complex transactions (multi-document) |
| Hierarchical data | Highly normalized data |
| Schema evolution | Strict schema enforcement |
| Content management | Heavy JOIN requirements |
| IoT / time-series | ACID-critical financial systems |

**Links**: [[SQL vs NoSQL Databases]] | [[Database Engines Compared]] | [[Redis Deep Dive]] | [[Cassandra]] | [[Data Modeling]]
