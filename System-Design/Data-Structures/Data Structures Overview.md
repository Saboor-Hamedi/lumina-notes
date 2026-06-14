---
id: a1b2c3d4-1136-4000-8000-000000000136
title: Data Structures Overview
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001136
---
# Data Structures Overview

A comprehensive reference of fundamental data structures, their properties, and use cases.

## Arrays

```javascript
const arr = [1, 2, 3, 4, 5];
```

| Operation | Time | Notes |
|-----------|------|-------|
| Access | O(1) | By index |
| Search | O(n) | Linear |
| Insert (end) | O(1)* | Amortized |
| Insert (middle) | O(n) | Shift elements |
| Delete | O(n) | Shift elements |

**Static vs Dynamic**: Static arrays have fixed size; dynamic arrays grow automatically (doubling strategy).

## Linked Lists

```javascript
class Node {
    constructor(value) { this.value = value; this.next = null; }
}

// Singly: Node → Node → null
// Doubly: null ⟷ Node ⟷ Node ⟷ null
// Circular: Node → Node → (back to head)
```

| Operation | Singly | Doubly |
|-----------|--------|--------|
| Head insert | O(1) | O(1) |
| Tail insert | O(n) / O(1)* | O(1)* |
| Search | O(n) | O(n) |
| Delete (known node) | O(n) | O(1) |

\* With tail pointer.

## Stacks and Queues

```javascript
// Stack (LIFO)
class Stack {
    constructor() { this.items = []; }
    push(v) { this.items.push(v); }
    pop() { return this.items.pop(); }
    peek() { return this.items[this.items.length - 1]; }
}

// Queue (FIFO)
class Queue {
    constructor() { this.items = []; }
    enqueue(v) { this.items.push(v); }
    dequeue() { return this.items.shift(); } // O(n) — use linked list for O(1)
}
```

| Structure | Insert | Delete | Use Case |
|-----------|--------|--------|----------|
| Stack | O(1) push | O(1) pop | Undo, call stack, DFS |
| Queue | O(1) enqueue | O(1) dequeue | BFS, task scheduling |
| Priority Queue | O(log n) | O(log n) | Dijkstra, scheduling |

## Hash Tables

```javascript
const map = new Map();    // Any key type, insertion ordered
const set = new Set();    // Unique values
const obj = {};           // String keys only
```

| Operation | Average | Worst |
|-----------|---------|-------|
| Insert | O(1) | O(n) |
| Lookup | O(1) | O(n) |
| Delete | O(1) | O(n) |

Collision resolution: Chaining (linked list per bucket) or Open addressing (probing).

## Trees

```javascript
class TreeNode {
    constructor(value) {
        this.value = value;
        this.left = null;
        this.right = null;
    }
}
```

| Tree Type | Property | Use Case |
|-----------|----------|----------|
| Binary Search Tree | left < node < right | Searching |
| AVL Tree | Self-balancing (height diff ≤ 1) | Guaranteed O(log n) |
| Red-Black Tree | Self-balancing (color constraints) | TreeMap, std::map |
| B-Tree | Multiple children per node | Databases, filesystems |
| Trie | Prefix-based | Autocomplete, spell check |
| Segment Tree | Range queries | Interval queries |
| Fenwick Tree | Prefix sums | Cumulative frequency |

## Heaps

```javascript
// Min-Heap: parent < children
// Max-Heap: parent > children
class MinHeap {
    constructor() { this.heap = []; }
    insert(v) { this.heap.push(v); this._bubbleUp(); }
    extract() { const min = this.heap[0]; this.heap[0] = this.heap.pop(); this._bubbleDown(); return min; }
    peek() { return this.heap[0]; }
}
```

| Operation | Time |
|-----------|------|
| Insert | O(log n) |
| Extract min/max | O(log n) |
| Peek | O(1) |
| Build | O(n) |

## Graphs

```javascript
// Adjacency list
const graph = new Map([
    ["A", ["B", "C"]],
    ["B", ["A", "D"]],
    ["C", ["A", "D"]],
    ["D", ["B", "C"]]
]);

class Graph {
    constructor() { this.adj = new Map(); }
    addEdge(u, v) {
        if (!this.adj.has(u)) this.adj.set(u, []);
        this.adj.get(u).push(v);
    }
}
```

| Representation | Space | Edge Check | Neighbors |
|---------------|-------|------------|-----------|
| Adjacency Matrix | O(V²) | O(1) | O(V) |
| Adjacency List | O(V+E) | O(deg(v)) | O(deg(v)) |

## Selection Guide

| Need | Structure |
|------|-----------|
| Fast lookups by key | Hash table |
| Ordered data, fast search | Balanced BST |
| FIFO processing | Queue |
| LIFO processing | Stack |
| Priority-based processing | Heap |
| Hierarchical data | Tree |
| Relationships between items | Graph |
| Fast prefix search | Trie |
| Range queries | Segment tree |
| Unique elements, fast membership | Hash set |

**Links**: [[Algorithm Paradigms]] | [[Computational Complexity]] | [[Sorting and Searching Algorithms]] | [[Graph Algorithms]] | [[Operating Systems]]
