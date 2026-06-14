---
id: a1b2c3d4-1176-4000-8000-000000000176
title: WebSocket Deep Dive
language: markdown
tags: [web-dev, websocket, protocol]
selection: null
isPinned: false
timestamp: 1781500001176
---

**Links**: [[WebSockets]] | [[WebRTC]] | [[HTTP Protocol]] | [[HTTP-3 and QUIC]] | [[API Gateway Patterns]] | [[Web Development Fundamentals]]


# WebSocket Deep Dive

WebSocket provides full-duplex communication over a single TCP connection. It's the foundation for real-time web applications.

## Protocol Handshake

```
Client (HTTP Upgrade)                    Server
    │                                         │
    │── GET /chat HTTP/1.1 ─────────────────►│
    │   Host: example.com                    │
    │   Upgrade: websocket                   │
    │   Connection: Upgrade                  │
    │   Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==    │
    │   Sec-WebSocket-Version: 13            │
    │                                         │
    │◄── 101 Switching Protocols ────────────│
    │   Upgrade: websocket                   │
    │   Connection: Upgrade                  │
    │   Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=  │
    │                                         │
    │◄══════ Full-duplex data frames ═══════►│
```

## Frame Format

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|F|R|R|R| opcode|M| Payload len | Extended payload length (16/64) |
|I|S|S|S|  (4)  |A|     (7)     |  (if payload len == 126/127)  |
|N|V|V|V|       |S|             |                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| Extended payload length continued (if 64-bit)                  |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| Masking-key (if MASK set, 32 bits)                             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| Payload Data (variable length)                                |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

**Opcode**: 0x1 (text), 0x2 (binary), 0x8 (close), 0x9 (ping), 0xA (pong)

## Server Implementation

```javascript
const WebSocket = require("ws");

const wss = new WebSocket.Server({ port: 8080 });

wss.on("connection", (ws, req) => {
    console.log(`Client connected from ${req.socket.remoteAddress}`);

    // Send welcome
    ws.send(JSON.stringify({ type: "connection", message: "Welcome!" }));

    // Handle messages
    ws.on("message", (data) => {
        try {
            const message = JSON.parse(data);
            handleMessage(ws, message);
        } catch (e) {
            ws.send(JSON.stringify({ type: "error", message: "Invalid JSON" }));
        }
    });

    // Handle pong
    ws.on("pong", () => {
        ws.isAlive = true;
    });

    // Handle close
    ws.on("close", (code, reason) => {
        console.log(`Client disconnected: ${code} ${reason}`);
    });
});

// Heartbeat (ping/pong) to detect dead connections
const interval = setInterval(() => {
    wss.clients.forEach((ws) => {
        if (ws.isAlive === false) return ws.terminate();
        ws.isAlive = false;
        ws.ping();
    });
}, 30000);

wss.on("close", () => clearInterval(interval));
```

## Client Implementation

```javascript
const ws = new WebSocket("wss://example.com/chat");

ws.onopen = () => {
    console.log("Connected");
    ws.send(JSON.stringify({ type: "join", room: "general" }));
};

ws.onmessage = (event) => {
    const message = JSON.parse(event.data);
    displayMessage(message);
};

ws.onclose = (event) => {
    console.log(`Disconnected: code=${event.code}, reason=${event.reason}`);
    // Reconnect with exponential backoff
    setTimeout(connect, Math.min(1000 * 2 ** retryCount, 30000));
};

ws.onerror = (error) => {
    console.error("WebSocket error:", error);
};
```

## Broadcasting

```javascript
// Broadcast to all connected clients
function broadcast(message, exclude = null) {
    const data = JSON.stringify(message);
    wss.clients.forEach((client) => {
        if (client !== exclude && client.readyState === WebSocket.OPEN) {
            client.send(data);
        }
    });
}

// Rooms/groups
const rooms = new Map();

function joinRoom(ws, roomName) {
    if (!rooms.has(roomName)) rooms.set(roomName, new Set());
    rooms.get(roomName).add(ws);
    ws.rooms = ws.rooms || new Set();
    ws.rooms.add(roomName);
}

function broadcastToRoom(roomName, message, exclude = null) {
    const room = rooms.get(roomName);
    if (!room) return;
    const data = JSON.stringify(message);
    room.forEach((client) => {
        if (client !== exclude && client.readyState === WebSocket.OPEN) {
            client.send(data);
        }
    });
}
```

## Backpressure

```javascript
// Handle slow consumers — avoid unbounded memory growth
ws.on("message", (data) => {
    const canSend = ws.bufferedAmount < MAX_BUFFER;

    if (!canSend) {
        // Client is too slow, apply backpressure
        ws.pause();  // or close connection
        return;
    }

    broadcast(data);
});
```

## Security

```javascript
// Origin check
wss.on("connection", (ws, req) => {
    const origin = req.headers.origin;
    if (!ALLOWED_ORIGINS.includes(origin)) {
        ws.close(1008, "Origin not allowed");
        return;
    }
});

// Rate limiting
const rateLimiter = new Map();

function checkRateLimit(ws, maxMessages = 60, windowMs = 60000) {
    const key = ws._socket.remoteAddress;
    const now = Date.now();

    if (!rateLimiter.has(key)) {
        rateLimiter.set(key, []);
    }

    const timestamps = rateLimiter.get(key).filter(t => now - t < windowMs);
    timestamps.push(now);
    rateLimiter.set(key, timestamps);

    return timestamps.length <= maxMessages;
}

// Authentication (verify token during handshake)
wss.on("connection", (ws, req) => {
    const token = new URL(req.url, "http://localhost").searchParams.get("token");
    if (!validateToken(token)) {
        ws.close(4001, "Unauthorized");
    }
});
```

## WebSocket vs Alternatives

| Feature | WebSocket | SSE | Long Polling |
|---------|-----------|-----|--------------|
| Direction | Bidirectional | Server → Client | Bidirectional |
| Protocol | ws:// | HTTP | HTTP |
| Reconnection | Manual | Automatic | Manual |
| Binary data | Full support | Limited | Base64 encoding |
| Browser support | Universal | Universal | Universal |
| Connection overhead | Low (after upgrade) | Minimal | High (per poll) |
