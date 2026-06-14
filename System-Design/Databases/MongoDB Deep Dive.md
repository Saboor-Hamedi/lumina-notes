---
id: a1b2c3d4-1184-4000-8000-000000000184
title: MongoDB Deep Dive
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001184
---
# MongoDB Deep Dive

MongoDB is a document-oriented NoSQL database with flexible schema, horizontal scaling, and rich query capabilities.

## Document Model

```json
// Single document — no joins needed
{
    "_id": ObjectId("668a1b2c3d4e5f6a7b8c9d0e"),
    "email": "alice@example.com",
    "name": "Alice",
    "address": {
        "street": "123 Main St",
        "city": "Portland",
        "state": "OR",
        "zip": "97201"
    },
    "orders": [
        { "order_id": 1001, "amount": 49.99, "date": ISODate("2026-06-01") },
        { "order_id": 1002, "amount": 29.99, "date": ISODate("2026-06-10") }
    ],
    "tags": ["premium", "wholesale"],
    "created_at": ISODate("2025-01-15T10:30:00Z")
}
```

## CRUD Operations

```javascript
// Create
db.users.insertOne({
    name: "Alice",
    email: "alice@example.com",
    age: 30,
    tags: ["premium"]
});

db.users.insertMany([
    { name: "Bob", email: "bob@example.com", age: 25 },
    { name: "Charlie", email: "charlie@example.com", age: 35 }
]);

// Read
db.users.findOne({ email: "alice@example.com" });
db.users.find({ age: { $gte: 25, $lte: 40 } }).sort({ name: 1 }).limit(10);
db.users.find({ tags: "premium", age: { $exists: true } });

// Update
db.users.updateOne(
    { email: "alice@example.com" },
    { $set: { age: 31 }, $push: { tags: "vip" } }
);

db.users.updateMany(
    { age: { $lt: 18 } },
    { $set: { status: "minor" } }
);

// Delete
db.users.deleteOne({ email: "bob@example.com" });
db.users.deleteMany({ last_login: { $lt: new Date("2025-01-01") } });
```

## Indexing

```javascript
// Single field
db.users.createIndex({ email: 1 });

// Compound
db.users.createIndex({ status: 1, created_at: -1 });

// Multikey (array fields)
db.users.createIndex({ tags: 1 });

// Text search
db.users.createIndex({ name: "text", bio: "text" });
db.users.find({ $text: { $search: "software engineer" } });

// Geospatial
db.places.createIndex({ location: "2dsphere" });
db.places.find({
    location: {
        $near: {
            $geometry: { type: "Point", coordinates: [-122.68, 45.52] },
            $maxDistance: 5000
        }
    }
});

// TTL (auto-expire)
db.sessions.createIndex({ created_at: 1 }, { expireAfterSeconds: 3600 });

// Sparse (index only documents that have the field)
db.users.createIndex({ optional_field: 1 }, { sparse: true });
```

## Aggregation Pipeline

```javascript
db.orders.aggregate([
    // Stage 1: Filter
    { $match: { status: "completed", created_at: { $gte: ISODate("2026-01-01") } } },

    // Stage 2: Group and compute
    { $group: {
        _id: "$customer_id",
        total_spent: { $sum: "$amount" },
        order_count: { $sum: 1 },
        avg_order: { $avg: "$amount" },
        first_order: { $min: "$created_at" }
    }},

    // Stage 3: Filter groups
    { $match: { total_spent: { $gte: 1000 } } },

    // Stage 4: Sort
    { $sort: { total_spent: -1 } },

    // Stage 5: Limit
    { $limit: 10 },

    // Stage 6: Lookup (join with another collection)
    { $lookup: {
        from: "customers",
        localField: "_id",
        foreignField: "customer_id",
        as: "customer"
    }},

    // Stage 7: Shape output
    { $project: {
        customer_id: "$_id",
        total_spent: 1,
        order_count: 1,
        customer_name: { $arrayElemAt: ["$customer.name", 0] }
    }}
]);
```

## Replica Sets

```javascript
// 3-member replica set
rs.initiate({
    _id: "rs0",
    members: [
        { _id: 0, host: "mongo1:27017", priority: 2 },
        { _id: 1, host: "mongo2:27017", priority: 1 },
        { _id: 2, host: "mongo3:27017", priority: 0, arbiterOnly: false }
    ]
});

// Read preferences
db.users.find().readPref("primary");             // Default
db.users.find().readPref("primaryPreferred");    // Primary, fallback to secondary
db.users.find().readPref("secondary");           // Read only from secondary
db.users.find().readPref("nearest");             // Lowest latency node
```

## Sharding

```javascript
// Enable sharding
sh.enableSharding("my_database");

// Shard key selection (critical for performance)
// Must be indexed, high cardinality, evenly distributed

// Range-based sharding
sh.shardCollection("my_database.users", { email: 1 });

// Hashed sharding (better distribution)
sh.shardCollection("my_database.users", { user_id: "hashed" });

// Zone-based sharding (data locality)
sh.addShardTag("shard01", "us-east");
sh.addShardTag("shard02", "us-west");

sh.updateZoneKeyRange(
    "my_database.users",
    { country: "US" },
    { country: "US\uffff" },
    "us-east"
);
```

## Change Streams

```javascript
// Real-time data change notifications
const changeStream = db.orders.watch([
    { $match: { "fullDocument.status": "pending" } }
]);

changeStream.on("change", (change) => {
    console.log("Order changed:", change);
    // change.operationType: "insert", "update", "replace", "delete"
    // change.fullDocument: the modified document (for insert/replace)
});
```

## Schema Design Patterns

| Pattern | When to Use | Example |
|---------|-------------|---------|
| Embedded | One-to-few relationships | Address in user profile |
| Reference | One-to-many or many-to-many | User → Orders (separate collection) |
| Bucket | Time-series data | Group sensor readings by hour |
| Polymorphic | Different shapes, same collection | Events with different types |
| Computed | Pre-computed aggregates | Monthly summary field |
| Subset | Large arrays, partial access | Last 10 comments in post |

## Performance Tuning

```javascript
// Explain query execution
db.users.find({ email: "alice@example.com" }).explain("executionStats");

// Profiling
db.setProfilingLevel(1, { slowms: 100 });  // Log queries > 100ms
db.system.profile.find().sort({ ts: -1 }).limit(10);

// Check index usage
db.users.aggregate([
    { $indexStats: {} }
]);

// Server status
db.serverStatus().connections;
db.serverStatus().opLatencies;
db.serverStatus().wiredTiger.cache;
```
