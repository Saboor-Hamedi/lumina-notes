---
id: a1b2c3d4-0005-4000-8000-000000000005
title: Data Structures
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781400000005
---

**Links**: [[Data Structures Overview]] | [[Probabilistic Data Structures]]


# Data Structures

Data structures organize and store data for efficient access and modification.

## Fundamental Structures

| Structure | Access | Search | Insert | Delete |
|-----------|--------|--------|--------|--------|
| Array | O(1) | O(n) | O(n) | O(n) |
| Linked List | O(n) | O(n) | O(1) | O(1) |
| Hash Table | O(1)* | O(1)* | O(1)* | O(1)* |
| Binary Search Tree | O(log n) | O(log n) | O(log n) | O(log n) |
| Stack | O(1) top | O(n) | O(1) push | O(1) pop |
| Queue | O(1) front/back | O(n) | O(1) enqueue | O(1) dequeue |
| Heap | O(1) min/max | O(n) | O(log n) | O(log n) |

*Average case; worst-case O(n) with collisions

## Advanced Structures

- **Trie**: String prefix search, autocomplete
- **Graph**: Networks, social connections, routes
- **Bloom Filter**: Probabilistic set membership
- **Segment Tree**: Range queries with updates
- **LRU Cache**: Bounded memory with eviction

## When to Use

| Need | Structure |
|------|-----------|
| Fast lookups by key | Hash Table |
| Ordered data | BST / Balanced Tree |
| FIFO processing | Queue |
| LIFO processing | Stack |
| Priority-based | Heap |
| String prefix search | Trie |
| Relationships | Graph |

**See also**: [[Big O Notation]], [[Programming Language Paradigms]], [[Code Architecture Patterns]]