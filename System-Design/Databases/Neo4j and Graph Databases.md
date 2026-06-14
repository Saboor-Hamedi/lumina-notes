---
id: a1b2c3d4-1185-4000-8000-000000000185
title: Neo4j and Graph Databases
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001185
---
# Neo4j and Graph Databases

Graph databases store data as nodes (entities) and relationships (connections), optimized for traversing connected data.

## Property Graph Model

```
(User:Person {name: "Alice"})
    │
    │─[:FRIENDS_WITH {since: 2020}]──►(User:Person {name: "Bob"})
    │
    │─[:WROTE {rating: 5}]──────────►(Post:Review {title: "Great Product"})
    │
    │─[:PURCHASED {date: "2026-06-01"}]──►(Product:Item {name: "Widget"})
                                             │
                                             │─[:CATEGORIZED_AS]──►(Category:Tag {name: "Electronics"})
```

**Node**: Entity with labels and properties.

**Relationship**: Directed connection with type and properties.

**Property**: Key-value pair on nodes or relationships.

## When to Use Graph Databases

| Good For | Not Good For |
|----------|-------------|
| Social networks | Simple CRUD |
| Recommendation engines | High-volume transactional |
| Fraud detection | Simple aggregations |
| Access control graphs | Flat tabular data |
| Knowledge graphs | Time-series |
| Network/infrastructure | Large-scale batch analytics |

## Cypher Query Language

```cypher
// Create nodes
CREATE (alice:Person {name: "Alice", age: 30})
CREATE (bob:Person {name: "Bob", age: 25})
CREATE (product:Product {name: "Widget", price: 49.99})

// Create relationships
CREATE (alice)-[:FRIENDS_WITH {since: 2020}]->(bob)
CREATE (alice)-[:PURCHASED {date: "2026-06-01", quantity: 2}]->(product)

// Find Alice's friends
MATCH (alice:Person {name: "Alice"})-[:FRIENDS_WITH]->(friend)
RETURN friend.name, friend.age

// Find friends of friends (2 levels deep)
MATCH (alice:Person {name: "Alice"})-[:FRIENDS_WITH*2]->(foaf)
RETURN DISTINCT foaf.name

// Shortest path
MATCH path = shortestPath(
    (alice:Person {name: "Alice"})-[:FRIENDS_WITH*]-(bob:Person {name: "Bob"})
)
RETURN path

// Product recommendations (friends also purchased)
MATCH (alice:Person {name: "Alice"})-[:PURCHASED]->(product)<-[:PURCHASED]-(other)
WHERE other <> alice
MATCH (other)-[:PURCHASED]->(recommendation)
WHERE NOT EXISTS((alice)-[:PURCHASED]->(recommendation))
RETURN recommendation.name, COUNT(*) AS frequency
ORDER BY frequency DESC
LIMIT 5
```

## Advanced Queries

```cypher
// Variable-length paths
MATCH (alice:Person {name: "Alice"})-[:MANAGES*1..5]->(report)
RETURN report.name, length(report) AS depth

// Aggregation in paths
MATCH (user:Person)-[:WROTE]->(review)-[:ABOUT]->(product)
RETURN product.name, AVG(review.rating) AS avg_rating, COUNT(review) AS review_count

// Conditional path creation
MERGE (alice:Person {name: "Alice"})
MERGE (bob:Person {name: "Bob"})
MERGE (alice)-[r:FRIENDS_WITH]->(bob)
ON CREATE SET r.since = 2026
ON MATCH SET r.last_interaction = timestamp()

// Delete all
MATCH (n) DETACH DELETE n
```

## Indexing

```cypher
// Single property index
CREATE INDEX person_name_idx FOR (p:Person) ON (p.name);

// Composite index
CREATE INDEX person_name_age_idx FOR (p:Person) ON (p.name, p.age);

// Text index
CREATE TEXT INDEX person_bio_idx FOR (p:Person) ON (p.bio);

// Full-text search
CALL db.index.fulltext.createNodeIndex("person_search", ["Person"], ["name", "bio"]);
CALL db.index.fulltext.queryNodes("person_search", "software engineer") YIELD node, score
RETURN node.name, score
```

## Performance Considerations

```cypher
// PROFILE query to see execution plan
PROFILE MATCH (alice:Person {name: "Alice"})-[:FRIENDS_WITH]->(friend)
RETURN friend

// Use labels to limit traversal
// BAD: MATCH (n)-[:FRIENDS_WITH]->(m)  (scans ALL nodes)
// GOOD: MATCH (p:Person)-[:FRIENDS_WITH]->(friend:Person)

// Use directed relationships when possible
// BAD: MATCH (a)-[:KNOWS]-(b)
// GOOD: MATCH (a)-[:KNOWS]->(b)  (direction known)
```

## Graph Algorithms

```python
from neo4j import GraphDatabase
from graphdatascience import GraphDataScience

# PageRank (influence/importance)
gds.pageRank.write(
    "my-graph",
    writeProperty: "pageRank"
)

# Betweenness Centrality (bridge nodes)
gds.betweenness.stream("my-graph")
    .sort("score", ascending=False)
    .limit(10)

# Community Detection (Louvain)
gds.louvain.write(
    "my-graph",
    writeProperty: "community"
)

# Similarity (Jaccard, Cosine)
gds.nodeSimilarity.stream("my-graph")
    .sort("similarity", ascending=False)
    .limit(20)
```

## Use Case: Access Control

```cypher
// Role-based access control graph
CREATE (alice:User {name: "Alice"})
CREATE (admin:Role {name: "admin"})
CREATE (editor:Role {name: "editor"})
CREATE (doc1:Document {id: "doc_123"})
CREATE (doc2:Document {id: "doc_456"})

CREATE (alice)-[:HAS_ROLE]->(admin)
CREATE (admin)-[:CAN_PERFORM]->(delete:Permission {action: "delete"})
CREATE (admin)-[:CAN_PERFORM]->(edit:Permission {action: "edit"})
CREATE (editor)-[:CAN_PERFORM]->(edit)

CREATE (document)-[:ALLOWS]->(edit)

// Can Alice delete doc_123?
MATCH (user:User {name: "Alice"})-[:HAS_ROLE]->(role)-[:CAN_PERFORM]->(perm:Permission {action: "delete"})
MATCH (doc:Document {id: "doc_123"})-[:ALLOWS]->(perm)
RETURN COUNT(*) > 0 AS can_delete
```

## Comparison

| Feature | Neo4j | PostgreSQL (recursive CTE) | ArangoDB |
|---------|-------|--------------------------|----------|
| Query language | Cypher | SQL WITH RECURSIVE | AQL |
| Graph model | Property graph | Relational with adj list | Multi-model |
| Path traversal | Native, fast | Recursive CTE | Multi-model |
| Graph algorithms | Built-in (GDS) | Manual implementation | Limited |
| ACID | Yes | Yes | Yes |
| Scaling | Single writer | Master-slave | Multi-model |
