---
id: dev-059-0000-0000-0000-000000000045
title: HTTP Caching
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781800000023
---
# HTTP Caching

**Links**: [[HTTP Protocol]] | [[Caching Strategies]] | [[REST API Design]] | [[Nginx Configuration]] | [[Web Security]]

## What is HTTP Caching?

HTTP caching stores responses to reduce latency, bandwidth, and server load. It's controlled by response headers.

## Cache Headers

```http
HTTP/1.1 200 OK
Cache-Control: public, max-age=3600, must-revalidate
ETag: "abc123"
Last-Modified: Mon, 15 Jan 2024 10:00:00 GMT
Expires: Mon, 15 Jan 2024 11:00:00 GMT
```

| Header | Purpose |
|--------|---------|
| `Cache-Control: max-age=3600` | Cache for 1 hour |
| `Cache-Control: no-cache` | Revalidate before use |
| `Cache-Control: no-store` | Never cache (sensitive data) |
| `Cache-Control: private` | Only cache in browser, not CDN |
| `Cache-Control: public` | Cache anywhere (CDN, proxy, browser) |
| `ETag: "hash"` | Version identifier |
| `Last-Modified: date` | Modification timestamp |

## Validation

### ETag (Strong Validation)

```http
# Request
If-None-Match: "abc123"

# Response (not modified)
304 Not Modified
ETag: "abc123"
```

### Last-Modified (Weak Validation)

```http
# Request
If-Modified-Since: Mon, 15 Jan 2024 10:00:00 GMT

# Response (not modified)
304 Not Modified
```

## Cache Strategies

| Strategy | `Cache-Control` | Use Case |
|----------|----------------|----------|
| Immutable | `public, max-age=31536000, immutable` | Versioned assets (app.js?v=2) |
| Fresh | `public, max-age=300` | API responses |
| Revalidate | `public, no-cache` | Dynamic content |
| Private | `private, max-age=3600` | User-specific content |
| No cache | `no-store` | Auth pages, sensitive data |

## Cache Invalidation

- **Time-based**: TTL expires
- **Event-based**: Purge cache on update (CDN API)
- **Versioned URLs**: `/static/app.v2.js` (cache busting)

**Next**: [[Rate Limiting]] — Protect your APIs