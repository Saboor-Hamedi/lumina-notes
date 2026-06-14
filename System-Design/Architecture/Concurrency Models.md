---
id: a1b2c3d4-0009-4000-8000-000000000009
title: Concurrency Models
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781400000009
---
# Concurrency Models

Concurrency enables multiple tasks to make progress simultaneously, improving throughput and responsiveness.

## Models Compared

| Model | Description | Languages | Best For |
|-------|-------------|-----------|----------|
| Threading | OS-managed concurrent execution | Java, C++, Python (GIL) | CPU-bound parallel work |
| Async/Await | Cooperative multitasking with event loop | JavaScript, Python, Rust | I/O-bound tasks |
| Coroutines | Lightweight cooperative routines | Go (goroutines), Kotlin, Lua | High-concurrency servers |
| Actor Model | Isolated actors communicating via messages | Erlang, Elixir, Akka | Distributed systems |
| CSP | Communicating Sequential Processes | Go (channels) | Message-passing concurrency |

## Python Async Example

```python
import asyncio

async def fetch_data(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.json()

async def main():
    tasks = [fetch_data(url) for url in urls]
    results = await asyncio.gather(*tasks)
```

## Pitfalls

- **Race conditions**: Unsynchronized shared state
- **Deadlocks**: Circular resource waiting
- **Starvation**: Low-priority tasks never execute
- **GIL (Python)**: Only one thread executes Python bytecode

**See also**: [[Functional Programming]], [[Code Architecture Patterns]], [[Error Handling Patterns]]