---
id: new-018-0000-0000-0000-000000000018
title: Graph Databases
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781344421535
---
# Graph Databases

**Links**: [[Database Engines Compared]] | [[DB Relationship Patterns]] | [[Data Structures]] | [[Code Architecture Patterns]] | [[SQL JOIN Operations]]

## What is a Graph Database?

A graph database uses nodes (entities), relationships (edges), and properties to represent and store data. It prioritizes relationships — unlike relational databases where JOINs are expensive, graph traversals are fast.

## Property Graph Model

```
      ┌──────────┐           ┌──────────┐
      │  Person   │           │  Person   │
      │ name: Alice│─────────→│ name: Bob │
      │ age: 30   │  KNOWS   │ age: 28   │
      └─────┬─────┘           └──────────┘
            │                      │
            │ WORKS_AT             │ WORKS_AT
            ↓                      ↓
      ┌──────────┐           ┌──────────┐
      │ Company  │           │  City    │
      │ name: Acme│←──LOCATED_IN──────────┘
      └──────────┘
```

## Neo4j + Cypher Query Language

```cypher
// Create nodes and relationships
CREATE (alice:Person {name: 'Alice', age: 30})
CREATE (bob:Person {name: 'Bob', age: 28})
CREATE (acme:Company {name: 'Acme Corp'})
CREATE (alice)-[:KNOWS]->(bob)
CREATE (alice)-[:WORKS_AT]->(acme)
CREATE (bob)-[:WORKS_AT]->(acme)

// Query: Find friends of Alice who work at the same company
MATCH (alice:Person {name: 'Alice'})-[:KNOWS]-(friend)
WHERE (friend)-[:WORKS_AT]->(:Company)<-[:WORKS_AT]-(alice)
RETURN friend.name

// Shortest path between two people
MATCH path = shortestPath(
  (alice:Person {name: 'Alice'})-[:KNOWS*]-(bob:Person {name: 'Bob'})
)
RETURN path
```

## Relational vs Graph

| Query | Relational (SQL) | Graph (Cypher) |
|-------|-----------------|----------------|
| Friends of friends | 3 JOINs | `-[:KNOWS*2]-` |
| Recommendation chain | Recursive CTE | `-[:BOUGHT*1..3]-` |
| Shortest path | Impossible (natively) | `shortestPath()` |

## Use Cases

| Domain | Example |
|--------|---------|
| Social networks | Friends, followers, recommendations |
| Fraud detection | Transaction patterns, rings |
| Recommendation engines | "Customers who bought X also bought Y" |
| Knowledge graphs | Wikipedia, enterprise data |
| Network/IT | Infrastructure dependencies |
| Supply chain | Parts, assemblies, shipments |

**Next**: [[Build Tools]] — Bundlers and task runners
