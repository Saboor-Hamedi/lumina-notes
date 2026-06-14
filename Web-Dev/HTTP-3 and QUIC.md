---
id: a1b2c3d4-1166-4000-8000-000000000166
title: HTTP-3 and QUIC
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001166
---
# HTTP/3 and QUIC

HTTP/3 is the next major version of HTTP, using QUIC (Quick UDP Internet Connections) as its transport layer instead of TCP.

## Protocol Stack

```
HTTP/2                      HTTP/3
  │                           │
  ├── TLS 1.2/1.3 (optional)  ├── QUIC (includes TLS 1.3)
  ├── TCP                     ├── UDP
  ├── IP                      └── IP
  └── ...
```

| Version | Transport | Encryption | RFC |
|---------|-----------|------------|-----|
| HTTP/1.1 | TCP | Optional | 7230 |
| HTTP/2 | TCP | Optional (h2c) or TLS (h2) | 7540 |
| HTTP/3 | QUIC (UDP) | Mandatory (TLS 1.3) | 9114 |

## QUIC Features

### 0-RTT Connection Establishment

```
TCP + TLS 1.3:
  ── SYN ──►
  ◄── SYN-ACK ──
  ── ACK ──► (TCP 1-RTT)
  ── ClientHello ──►
  ◄── ServerHello + Finished ── (TLS 1-RTT)
  ── Finished ──►
  Total: 2 RTT

QUIC:
  ── Initial + Handshake ──►
  ◄── Handshake + 1-RTT ──
  Total: 1 RTT (0-RTT with cached credentials)
```

### Connection Migration

```
TCP: Connection dies if IP:port changes
    (Switching Wi-Fi → Cellular = connection reset)

QUIC: Connection identified by Connection ID, not IP:port
    (Switching Wi-Fi → Cellular = seamless continuation)
```

### Head-of-Line Blocking Elimination

```
HTTP/2 (TCP):
  Stream 1: [packet lost...]
  Stream 2: [waiting...]
  Stream 3: [waiting...]
  Stream 4: [waiting...]

  All streams blocked by one lost packet in TCP

QUIC:
  Stream 1: [packet lost...]
  Stream 2: ✓
  Stream 3: ✓
  Stream 4: ✓

  Only the affected stream is delayed
```

## HTTP/3 Frames

Frames are similar to HTTP/2 but carried over QUIC streams:

| Frame Type | Purpose |
|------------|---------|
| DATA | Payload data |
| HEADERS | HTTP headers (QPACK encoded) |
| PRIORITY | Stream priority |
| SETTINGS | Connection-level settings |
| GOAWAY | Graceful shutdown |
| CANCEL_PUSH | Server push cancellation |
| QPACK Encoder/Decoder Stream | Header compression |

## QPACK (Header Compression)

HTTP/3 uses QPACK instead of HPACK (HTTP/2):

```
HPACK: Assumes ordered delivery (TCP)
  Encoder and decoder must be in sync

QPACK: Works over unordered streams (QUIC)
  - Encoder stream: sends table updates
  - Decoder stream: sends acknowledgments
  - Request streams: reference table state
  - Allows parallel stream processing
```

## Stream Types

```
QUIC Stream IDs:
  - Client-initiated: odd numbers (1, 3, 5...)
  - Server-initiated: even numbers (0, 2, 4...)

Special streams:
  - Stream 0: Control stream (SETTINGS, GOAWAY, PRIORITY)
  - Stream 2: QPACK encoder stream
  - Stream 3: QPACK decoder stream (client-to-server)
  - Stream 6: QPACK decoder stream (server-to-client)
```

## Connection Migration

```python
# QUIC connection ID allows migration
# Client connects via Wi-Fi:
conn_id = "abc123"
source = ("192.168.1.5", 54321)
destination = ("203.0.113.1", 443)

# Client switches to cellular:
source = ("10.0.0.5", 65432)  # Changed!
destination = ("203.0.113.1", 443)  # Same

# Server sees new source, same conn_id → continues connection
# No re-handshake required
```

## Performance Benefits

| Scenario | TCP + TLS 1.3 | QUIC + TLS 1.3 | Benefit |
|----------|---------------|----------------|---------|
| New connection (cold) | 2 RTT | 1 RTT | 50% faster |
| Resumed connection (warm) | 1 RTT | 0 RTT | Instant |
| Packet loss (1%) | Up to 3x latency | ~1.1x latency | 2.7x faster |
| Network handover | Connection lost | Seamless | No interruption |

## Deployment Status

| Service | HTTP/3 Status |
|---------|---------------|
| Google | 100% (gRPC, YouTube, Search) |
| Facebook | 75% of traffic |
| Cloudflare | All customers enabled |
| Fastly | Enabled by default |
| Netflix | Enabled |
| YouTube | Enabled |

## Server Implementation

```nginx
# Nginx HTTP/3
server {
    listen 443 quic reuseport;       # QUIC + HTTP/3
    listen 443 ssl;                  # HTTP/2 fallback
    http2 on;

    ssl_certificate cert.pem;
    ssl_certificate_key key.pem;

    # Enable 0-RTT
    ssl_early_data on;

    # Add Alt-Svc header to advertise HTTP/3
    add_header Alt-Svc 'h3=":443"; ma=86400';
}
```

## Client-Side

```javascript
// Browser automatically uses HTTP/3 if server supports it
// Check via DevTools → Network → Protocol column (shows "h3")

// Fetch API works identically regardless of HTTP version
const response = await fetch("https://example.com/api");
```

## Limitations

| Issue | Impact | Mitigation |
|-------|--------|------------|
| UDP throttling | Some networks rate-limit UDP | Fallback to TCP |
| Middlebox interference | Corporate firewalls block UDP | HTTP/2 fallback (Alt-Svc) |
| CPU overhead (user-space) | Higher than kernel TCP | Hardware offload, optimization |
| Adoption | Servers/clients must upgrade | Graceful fallback via Alt-Svc |
