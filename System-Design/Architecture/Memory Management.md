---
id: a1b2c3d4-1013-4000-8000-000000000013
title: Memory Management
language: markdown
tags: [system-design, architecture, memory-management]
selection: null
isPinned: false
timestamp: 1781500001013
---
# Memory Management

Memory management controls how programs allocate, use, and release memory. Different languages use different strategies.

## Stack vs Heap

| Aspect | Stack | Heap |
|--------|-------|------|
| Allocation | Automatic (function calls) | Manual or GC |
| Size | Fixed (per thread, ~1-8 MB) | Large (GBs) |
| Speed | Fast (push/pop) | Slower (allocation, fragmentation) |
| Lifetime | Function scope | Until freed or GC'd |
| Data size | Known at compile time | Dynamic |

## Garbage Collection Algorithms

| Algorithm | How It Works | Pros | Cons |
|-----------|-------------|------|------|
| Reference Counting | Count references, free when 0 | Simple, predictable | Cannot handle cycles |
| Mark-and-Sweep | Trace from roots, mark reachable, sweep rest | Handles cycles | Pauses program |
| Copying (Cheney) | Divide heap in two, copy live objects | No fragmentation | Uses 2x memory |
| Generational | New objects collected more often | Efficient (most die young) | Tuning needed |
| Concurrent | GC runs in parallel with program | Low pause times | More CPU overhead |

## Manual Memory Management (C)

```c
int* arr = (int*)malloc(10 * sizeof(int));
// use arr...
free(arr);  // must free exactly once
```

## RAII (C++)

Resource Acquisition Is Initialization — destructors run automatically when objects go out of scope:

```cpp
std::vector<int> v; // allocates on heap
// ... use v ...
// automatically freed when v goes out of scope
```

## Ownership (Rust)

Rust's borrow checker enforces memory safety at compile time:

```rust
let s = String::from("hello"); // s owns the string
let t = s; // ownership moves to t, s is invalid
```

## Automatic Reference Counting (Swift/ObjC)

```swift
class User {
    var name: String
    weak var pet: Pet? // weak avoids retain cycles
}
```

## Memory Leaks

Common causes:
- Unreferenced objects still reachable (stuck in a collection)
- Event listeners never unregistered
- Circular references in ref-counted systems
- Cached data without eviction
- Closure capturing large objects

## Memory Profiling Tools

| Tool | Platform | Use |
|------|----------|-----|
| Valgrind | Linux | Detects leaks and errors |
| heaptrack | Linux | Heap profiler |
| Instruments | macOS/iOS | Allocation tracking |
| dotMemory | .NET | Memory analysis |
| Chrome DevTools | Web | Heap snapshots, allocation timeline |

**Links**: [[Operating Systems]] | [[Performance Profiling]] | [[Data Structures]] | [[Concurrency Models]] | [[Python Imports and Modules]]
