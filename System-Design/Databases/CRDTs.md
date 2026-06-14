---
id: a1b2c3d4-1139-4000-8000-000000000139
title: CRDTs
language: markdown
tags: [system-design, databases, crdt, conflict-resolution]
selection: null
isPinned: false
timestamp: 1781500001139
---

**Links**: [[Event Sourcing Deep Dive]] | [[Consistent Hashing]] | [[Database Replication Strategies]] | [[Database Transactions]] | [[Event Sourcing]] | [[UUID vs Snowflake vs ULID]]


# CRDTs (Conflict-Free Replicated Data Types)

CRDTs are data structures that allow multiple replicas to be updated concurrently and independently, then automatically merged into a consistent state without conflicts.

## Why CRDTs

```
Traditional conflict resolution:
  Replica A: SET x = 1     Replica B: SET x = 2
  ↓                               ↓
  Sync → Conflict → Need resolution (last-writer-wins, manual, etc.)

CRDT approach:
  Replica A: ADD 1 to set     Replica B: ADD 2 to set
  ↓                               ↓
  Sync → Automatic merge → {1, 2} (no conflict)
```

## Properties

- **Convergent**: All replicas converge to the same state given the same updates
- **Commutative**: Merging operations in any order produces the same result
- **Associative**: Merging order doesn't matter
- **Idempotent**: Applying the same update twice = applying it once

## State-Based CRDTs (CvRDTs)

Merge state directly — requires sending full or delta state.

```python
class GCounter:
    """Grow-only counter — only increments."""
    def __init__(self):
        self.counters = {}  # node_id → count

    def increment(self, node_id):
        self.counters[node_id] = self.counters.get(node_id, 0) + 1

    def value(self):
        return sum(self.counters.values())

    def merge(self, other):
        for node_id, count in other.counters.items():
            self.counters[node_id] = max(self.counters.get(node_id, 0), count)


class PNCounter:
    """Positive-Negative counter — supports increments and decrements."""
    def __init__(self):
        self.pos = GCounter()
        self.neg = GCounter()

    def increment(self, node_id):
        self.pos.increment(node_id)

    def decrement(self, node_id):
        self.neg.increment(node_id)

    def value(self):
        return self.pos.value() - self.neg.value()

    def merge(self, other):
        self.pos.merge(other.pos)
        self.neg.merge(other.neg)
```

## Operation-Based CRDTs (CmRDTs)

Send only operations — requires reliable broadcast with causal ordering.

```python
class ORSet:
    """Observed-Remove Set — supports add and remove."""
    def __init__(self):
        self.elements = {}  # element → {tag}

    def add(self, element):
        tag = uuid.uuid4()
        self.elements.setdefault(element, set()).add(tag)
        return ("add", element, tag)

    def remove(self, element):
        tags = self.elements.pop(element, set())
        return ("remove", element, tags)

    def apply(self, operation):
        op_type, element, tags = operation
        if op_type == "add":
            self.elements.setdefault(element, set()).add(tags)
        elif op_type == "remove":
            # Only remove tags known at remove time
            if element in self.elements:
                self.elements[element] -= tags
                if not self.elements[element]:
                    del self.elements[element]

    def __contains__(self, element):
        return element in self.elements and bool(self.elements[element])
```

## Common CRDT Types

| Type | Description | Converges To |
|------|-------------|--------------|
| G-Counter | Grow-only counter | Sum of all increments |
| PN-Counter | Positive-negative counter | Net count |
| G-Set | Grow-only set | Union of all adds |
| 2P-Set | Two-phase set | Add then remove (no re-add) |
| LWW-Register | Last-writer-wins register | Latest timestamp wins |
| MV-Register | Multi-value register | Set of concurrent values |
| OR-Set | Observed-remove set | Set (supports re-add) |
| RGA | Replicated growable array | Ordered list (collaborative editing) |

## LWW-Register

```python
class LWWRegister:
    def __init__(self):
        self.value = None
        self.timestamp = 0
        self.node_id = None

    def set(self, value, timestamp, node_id):
        if (timestamp > self.timestamp) or \
           (timestamp == self.timestamp and node_id > self.node_id):
            self.value = value
            self.timestamp = timestamp
            self.node_id = node_id

    def merge(self, other):
        self.set(other.value, other.timestamp, other.node_id)

    def get(self):
        return self.value
```

## Collaborative Text Editing with RGA

RGA (Replicated Growable Array) enables Google Docs-style collaboration:

```javascript
// Each insert creates a node with:
// - ID: (timestamp, client_id) — globally unique
// - Content: the inserted character
// - Parent: ID of the preceding node
// - Position between: half-open interval of parent and next sibling

class RGANode {
    constructor(id, content, parentId) {
        this.id = id;          // (tombstone removed)
        this.content = content;
        this.parentId = parentId;
        this.isDeleted = false;
    }
}

// Merge rule:
// 1. Insert based on parent relationship
// 2. If same parent, order by ID
// 3. Tombstones remain (don't delete physically)
```

## Conflict Resolution Strategies

| Strategy | Example | CRDT Type |
|----------|---------|-----------|
| Last-writer-wins | LWW-Register | Timestamp comparison |
| Add-wins | OR-Set | Concurrent add beats remove |
| Remove-wins | Remove-wins set | Concurrent remove beats add |
| Merge | G-Counter | max or sum |
| Interleaving | RGA | Deterministic ordering |

## Practical Use Cases

| Use Case | CRDT | Examples |
|----------|------|----------|
| Collaborative editing | RGA, OR-Set | Google Docs, Etherpad |
| Distributed counters | PN-Counter | Analytics, view counts |
| Shopping cart | OR-Set | Add/remove items |
| User profiles | LWW-Register | Last-writer-wins |
| DNS records | Various | Multi-master DNS |
| Mobile data sync | Various | Realm, Couchbase Lite |

## Trade-offs

| Pro | Con |
|-----|-----|
| No conflict resolution needed | Storage overhead (tombstones, metadata) |
| Works offline | Eventual consistency (not strong) |
| No central coordinator | Complex to implement correctly |
| Automatic merge | Can grow unbounded (GC needed) |

## Libraries

| Library | Language | Features |
|---------|----------|----------|
| Automerge | JS/Rust | JSON-like CRDT, collaborative editing |
| Yjs | JS | High-performance text editing CRDT |
| Riak | Erlang | Distributed database with CRDTs |
| Redis CRDTs | C | Redis with CRDT-based replication |
| CRDT-rs | Rust | CRDT implementations in Rust |

**Links**: [[Distributed Systems Design]] | [[Raft Consensus Algorithm]] | [[Event-Driven Architecture]] | [[Consistent Hashing]] | [[System Design Fundamentals]]
