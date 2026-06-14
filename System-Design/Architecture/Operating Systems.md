---
id: a1b2c3d4-1003-4000-8000-000000000003
title: Operating Systems
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001003
---
# Operating Systems

An operating system manages hardware resources, provides abstractions for applications, and ensures isolation between processes.

## Process vs Thread

| Aspect | Process | Thread |
|--------|---------|--------|
| Address Space | Private | Shared with process |
| Overhead | High (fork, copy) | Low |
| Isolation | Fully isolated | Within-process shared |
| Communication | IPC (pipes, sockets) | Shared memory |
| Debugging | Independent | Can affect siblings |

## CPU Scheduling Algorithms

| Algorithm | Strategy | Starvation? |
|-----------|----------|-------------|
| FCFS | First come, first served | No |
| SJF | Shortest job first | Yes (long jobs) |
| Round Robin | Time slices (quantum) | No |
| Priority | Higher priority runs first | Yes (low priority) |
| Multi-Level Queue | Different queues per priority | Possible |

## Memory Management

- **Paging**: Fixed-size pages mapped to physical frames
- **Segmentation**: Variable-size logical segments
- **Virtual Memory**: Illusion of more RAM via disk swap
- **TLB**: Hardware cache for page table lookups

```
Virtual Address → Page Table → Physical Address
```

## Process States

```
New → Ready ↔ Running → Terminated
            ↓
         Waiting (I/O)
```

## Deadlock Conditions (all 4 required)

1. **Mutual Exclusion**: Resources are non-sharable
2. **Hold and Wait**: Process holds resources while waiting
3. **No Preemption**: Resources released voluntarily
4. **Circular Wait**: Circular chain of processes waiting

## IPC Mechanisms

| Method | Type | Use Case |
|--------|------|----------|
| Pipe | Byte stream | Parent-child processes |
| Socket | Network/stream | Cross-machine |
| Shared Memory | Fast direct access | High throughput |
| Message Queue | Structured messages | Decoupled services |
| Semaphore | Synchronization | Mutual exclusion |

## System Calls

Key categories: process control (fork, exec), file management (open, read, write), device management (ioctl), information (getpid), communication (pipe, socket).

**Links**: [[Concurrency Models]] | [[Memory Management]] | [[Docker Containers]] | [[Performance Profiling]]
