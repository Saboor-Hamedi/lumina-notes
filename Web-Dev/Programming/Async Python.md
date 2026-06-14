---
id: dev-068-0000-0000-0000-000000000057
title: Async Python
language: markdown
tags: [web-dev, programming, python, async]
selection: null
isPinned: false
timestamp: 1781900000009
---
# Async Python

**Links**: [[Concurrency Models]] | [[Python Imports and Modules]] | [[Unit Testing Guide]] | [[API Testing]] | [[WebSockets]]

## What is Async?

Async Python uses cooperative multitasking to handle I/O-bound tasks efficiently without threads.

## asyncio Basics

```python
import asyncio
import aiohttp

async def fetch_url(session, url):
    async with session.get(url) as response:
        return await response.text()

async def main():
    urls = ["https://api.example.com/a", "https://api.example.com/b"]

    async with aiohttp.ClientSession() as session:
        tasks = [fetch_url(session, url) for url in urls]
        results = await asyncio.gather(*tasks)

    for result in results:
        print(f"Fetched {len(result)} bytes")

asyncio.run(main())
```

## Async vs Sync

```python
# Synchronous (slow)
def fetch_all_sync():
    results = []
    for url in urls:
        results.append(requests.get(url).text)
    return results  # ~3 seconds for 3 URLs

# Asynchronous (fast)
async def fetch_all_async():
    async with aiohttp.ClientSession() as session:
        tasks = [fetch_url(session, url) for url in urls]
        return await asyncio.gather(*tasks)  # ~1 second for 3 URLs
```

## Key Async/Await

| Keyword | Purpose |
|---------|---------|
| `async def` | Define a coroutine |
| `await` | Pause until coroutine completes |
| `async with` | Async context manager |
| `async for` | Async iteration |
| `asyncio.gather()` | Run multiple coroutines concurrently |
| `asyncio.create_task()` | Schedule task for concurrent execution |

## Async Web Server (FastAPI)

```python
from fastapi import FastAPI
import asyncio

app = FastAPI()

@app.get("/slow")
async def slow_endpoint():
    await asyncio.sleep(5)  # Non-blocking
    return {"status": "done"}

@app.get("/fast")
async def fast_endpoint():
    return {"status": "instant"}
```

## Common Async Libraries

| Library | Purpose |
|---------|---------|
| aiohttp | HTTP client/server |
| httpx | HTTP client (sync+async) |
| asyncpg | PostgreSQL driver |
| aioredis | Redis client |
| aiofiles | File operations |
| FastAPI | Web framework |

**Next**: [[Python Type Hints]] — Static typing in Python