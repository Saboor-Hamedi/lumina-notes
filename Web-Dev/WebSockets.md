---
id: a1b2c3d4-0014-4000-8000-000000000014
title: WebSockets
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781400000014
---
# WebSockets

WebSockets provide full-duplex communication over a single TCP connection, enabling real-time data flow.

## HTTP vs WebSocket

```
HTTP:  Client ──request──→ Server ──response──→ Client (closes)
WS:   Client ──upgrade──→ Server ↔ bidirectional ↔ persistent
```

## Server Example (Python + FastAPI)

```python
from fastapi import FastAPI, WebSocket

app = FastAPI()

@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    while True:
        data = await websocket.receive_text()
        await websocket.send_text(f"Echo: {data}")
```

## Use Cases

- Live chat / instant messaging
- Real-time dashboards
- Collaborative editing (Google Docs-style)
- Gaming / multiplayer state sync
- Live notifications

**Links**: [[WebSocket Deep Dive]] | [[HTTP Protocol]] | [[REST API Design]] | [[Concurrency Models]] | [[Event-Driven Architecture]] | [[Message Queues]] | [[WebRTC]]

**See also**: [[Microservices Architecture]], [[API Gateway Patterns]], [[gRPC]]