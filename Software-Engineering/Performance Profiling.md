---
id: a1b2c3d4-1018-4000-8000-000000000018
title: Performance Profiling
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001018
---
# Performance Profiling

Profiling measures where a program spends time and memory, identifying bottlenecks to optimize.

## Types of Profiling

| Type | Measures | Tools |
|------|----------|-------|
| CPU | Time spent in functions | perf, vtune, py-spy |
| Memory | Heap allocations, leaks | Valgrind, heaptrack, tracemalloc |
| I/O | Disk/network wait time | iostat, strace |
| Lock contention | Thread synchronization | mutrace, lockstat |
| Cache misses | CPU cache efficiency | perf stat, cachegrind |

## Sampling vs Instrumentation

| Approach | How | Overhead |
|----------|-----|----------|
| Sampling | Periodic snapshots of call stack | Low (1-5%) |
| Instrumentation | Hooks on every function call | High (10-100x) |
| Event-based | Hooks at specific events (alloc, syscall) | Medium |

## Flame Graphs

Visualize where time is spent:

```
  ██████████████████████████████████████████████████
  ████ process_request █████████████████████████████
  ████ query_database ████ validate ████ serialize █
  ██ parse ██ execute ██ fetch ██ auth ██ check ██ to_
```

- **Width**: proportional to time spent
- **Color**: not significant (or red = native, orange = Python, etc.)
- **Y-axis**: call stack depth
- **Bottleneck**: wide top-level frames

## Profiling Workflow

1. **Establish baseline**: Measure current performance
2. **Set a goal**: e.g., reduce P99 latency by 50%
3. **Profile**: Find the bottleneck
4. **Hypothesize**: Why is this slow?
5. **Optimize**: Fix the most impactful bottleneck
6. **Measure again**: Verify improvement
7. **Repeat**: Next bottleneck

## Common Bottlenecks

| Bottleneck | Symptom | Fix |
|------------|---------|-----|
| N+1 queries | Many DB queries in loop | Batch queries, eager loading |
| Inefficient algorithm | CPU-bound O(n²) | Better data structure |
| Disk I/O | High I/O wait time | Cache, SSD, async writes |
| Lock contention | Threads waiting | Reduce shared state, shard |
| Memory churn | Excessive GC | Object pooling, reuse |
| Serialization | Large JSON | Binary format, compression |

## Key Metrics

| Metric | Meaning | Good Target |
|--------|---------|-------------|
| Latency | Time per request | P99 < 200ms |
| Throughput | Requests per second | Depends on system |
| CPU usage | % time busy | < 80% sustained |
| Memory usage | Heap + stack | < 80% of available |
| GC pause | Time stopped for GC | < 10ms per minute |
| I/O wait | Time waiting for disk/network | < 5% of CPU time |

## Profiling in Production

- Use low-overhead sampling profilers (py-spy, perf)
- Always profile under realistic load
- Profile both synthetic benchmarks and production traffic
- Use continuous profiling (Parca, Pyroscope, Google CPP)
- Profile after deployments to detect regressions

**Links**: [[SQL Query Optimization]] | [[Caching Strategies]] | [[Memory Management]] | [[Database Indexing Deep Dive]] | [[Load Testing]]
