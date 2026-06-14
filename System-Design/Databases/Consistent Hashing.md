---
id: a1b2c3d4-1153-4000-8000-000000000153
title: Consistent Hashing
language: markdown
tags: [system-design, databases, consistent-hashing, distributed-systems]
selection: null
isPinned: false
timestamp: 1781500001153
---

**Links**: [[Database Sharding]] | [[Database Replication Strategies]] | [[Caching Strategies]] | [[UUID vs Snowflake vs ULID]] | [[CRDTs]] | [[Database Engines Compared]]


# Consistent Hashing

Consistent hashing is a technique that distributes data across nodes while minimizing rebalancing when nodes are added or removed. Used by DynamoDB, Cassandra, Discord, and CDNs.

## Problem with Simple Hashing

```
hash(key) % N nodes

When N changes (node added/removed): almost ALL keys remap → massive data movement
```

## Consistent Hashing Solution

```
Hash ring (0 to 2^32-1):
               0
        ┌──────┴──────┐
        │             │
        │   Node A    │
        │             │
        │    Node     │
        │     D       │
        │             │
        │             │
        └──────┬──────┘
              2^32

Key placement: hash(key) → position on ring → first node clockwise
```

```python
import hashlib

class ConsistentHashRing:
    def __init__(self, nodes=None, replicas=3):
        self.replicas = replicas
        self.ring = {}         # hash → node
        self.sorted_keys = []  # sorted list of hash values
        self.nodes = set()

        if nodes:
            for node in nodes:
                self.add_node(node)

    def _hash(self, key):
        return int(hashlib.sha256(str(key).encode()).hexdigest(), 16)

    def add_node(self, node):
        self.nodes.add(node)
        for i in range(self.replicas):
            hash_val = self._hash(f"{node}:{i}")
            self.ring[hash_val] = node
            self.sorted_keys.append(hash_val)
        self.sorted_keys.sort()

    def remove_node(self, node):
        self.nodes.discard(node)
        for i in range(self.replicas):
            hash_val = self._hash(f"{node}:{i}")
            del self.ring[hash_val]
            self.sorted_keys.remove(hash_val)

    def get_node(self, key):
        if not self.ring:
            return None
        hash_val = self._hash(key)
        for h in self.sorted_keys:
            if hash_val <= h:
                return self.ring[h]
        return self.ring[self.sorted_keys[0]]  # wrap around
```

## Virtual Nodes (Replicas)

Without virtual nodes, an uneven distribution creates hot spots. Virtual nodes spread each physical node's data across the ring.

```
Physical nodes: A, B, C

Without replicas (3 virtual nodes per physical):
  A1, A2, A3, B1, B2, B3, C1, C2, C3 (interleaved on ring)

Better distribution, especially with few physical nodes.
```

## Example: Adding a Node

```
Before: N = 3 nodes, 30 keys

After: N = 4 nodes, 30 keys

Simple hashing: ~90% of keys move (30 * 3/4 ≈ 22.5 keys)
Consistent hashing: ~25% of keys move (30 / 4 ≈ 7.5 keys)
```

## Applications

| Application | Use Case |
|-------------|----------|
| Cassandra | Data partitioning across nodes |
| DynamoDB | Hash-based partitioning |
| Discord | Sharding voice/guild data |
| CDNs | Cache server selection |
| Load balancers | Sticky sessions without central store |
| Distributed caches | Memcached, Redis cluster |

## Comparison Table

| Aspect | Simple Hashing | Consistent Hashing |
|--------|---------------|-------------------|
| Remapping on node change | O(N) keys | O(1/N) keys |
| Lookup cost | O(1) | O(log N) |
| Implementation | Trivial | Moderate |
| Hot spot handling | None | Virtual nodes |
| Data skew | Possible | Minimized |

## Ring Size and Load Distribution

```python
import statistics

def measure_load_distribution(num_nodes, num_keys, replicas=3):
    ring = ConsistentHashRing(nodes=[f"node_{i}" for i in range(num_nodes)], replicas=replicas)
    loads = {node: 0 for node in ring.nodes}

    for i in range(num_keys):
        node = ring.get_node(f"key_{i}")
        loads[node] += 1

    loads_list = list(loads.values())
    return {
        "min": min(loads_list),
        "max": max(loads_list),
        "mean": statistics.mean(loads_list),
        "stdev": statistics.stdev(loads_list),
    }

# Few nodes, no replicas → high variance
print(measure_load_distribution(3, 10000, 1))
# → stdev ~40% of mean

# Few nodes, with replicas → balanced
print(measure_load_distribution(3, 10000, 100))
# → stdev ~5% of mean
```

**Links**: [[CAP Theorem and PACELC]] | [[Raft Consensus Algorithm]] | [[CRDTs]] | [[System Design Fundamentals]] | [[Database Sharding]]
