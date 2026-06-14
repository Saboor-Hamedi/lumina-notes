---
id: a1b2c3d4-1107-4000-8000-000000000107
title: CDN Architecture
language: markdown
tags: [system-design, architecture, cdn]
selection: null
isPinned: false
timestamp: 1781500001107
---
# CDN Architecture

A Content Delivery Network (CDN) distributes content across geographically dispersed servers to reduce latency, offload origin traffic, and improve availability.

## How CDNs Work

```
User in Tokyo → DNS → Tokyo Edge (cache hit) → Content
                          ↓ (cache miss)
                   Tokyo Edge → Origin Server (US)
                          ↓
                   Cache content → Serve → Next hit from cache
```

## CDN Types

| Type | Content | Examples |
|------|---------|----------|
| Static CDN | Images, CSS, JS, video | Cloudflare, Fastly, CloudFront |
| Dynamic CDN | API responses, HTML | Cloudflare Workers, Fastly Compute |
| Download CDN | Large file delivery | Akamai NetStorage |
| Video CDN | Streaming (HLS, DASH) | JW Player, Mux, YouTube CDN |

## Key Components

| Component | Function |
|-----------|----------|
| Edge Server | Cache + serve content at edge locations |
| Origin Server | Source of truth for content |
| Point of Presence (PoP) | Data center with edge servers |
| Reverse Proxy | Accepts requests, serves from cache or origin |
| Load Balancer | Distribute requests across edge servers |
| DNS | Geo-routing to nearest PoP |

## Caching Strategies

### TTL-Based

```
Cache-Control: max-age=3600       → cache for 1 hour
Cache-Control: s-maxage=3600     → shared cache (CDN) TTL
Cache-Control: no-cache          → revalidate every request
Cache-Control: private           → don't cache on CDN
```

### Invalidation

```bash
# CloudFront invalidation
aws cloudfront create-invalidation \
    --distribution-id E123ABC \
    --paths "/images/*" "/assets/styles.css"

# Purge (Cloudflare)
curl -X POST "https://api.cloudflare.com/client/v4/zones/ZONEID/purge_cache" \
    -H "Authorization: Bearer $TOKEN" \
    -H "Content-Type: application/json" \
    -d '{"files":["https://example.com/style.css"]}'
```

### Cache Tags (Surrogate-Key)

```http
Surrogate-Key: product page product-123 user-456
```

Purge all content tagged with `product-123` in one API call.

## Dynamic Content Acceleration

For uncacheable content, CDNs optimize the origin connection:

| Technique | Benefit |
|-----------|---------|
| TCP optimizations | Fast connection setup, keep-alive |
| TLS termination | Client → CDN (optimal), CDN → Origin (persistent) |
| Route optimization | Real-time fastest path to origin |
| Connection pooling | Reuse connections across requests |
| Brotli compression | Better compression than gzip |

## Edge Computing

Run code at the CDN edge to avoid origin roundtrips:

```javascript
// Cloudflare Worker
export default {
  async fetch(request) {
    const url = new URL(request.url);

    // Custom logic at edge
    if (url.pathname === "/api/user") {
      const user = await KV.get(`user:${url.searchParams.get("id")}`);
      if (user) return new Response(user);
    }

    // Fallback to origin
    return fetch(request);
  }
}
```

## CDN Selection Criteria

| Criteria | Consideration |
|----------|--------------|
| Geographic coverage | Where are your users? |
| Pricing model | Bandwidth vs request-based |
| Cache purge | Speed of clearing outdated content |
| Edge compute | Need for custom logic |
| Security | DDoS protection, WAF, bot management |
| Origin protocol | HTTP/2, gRPC support |
| SLAs | Uptime guarantees |

## Security Features

| Feature | Protection |
|---------|------------|
| DDoS mitigation | Absorb large attacks (Tbps scale) |
| WAF | Filter SQL injection, XSS, OWASP top 10 |
| Bot management | Distinguish human vs automated traffic |
| IP reputation | Block known malicious IPs |
| Rate limiting | Per-IP, per-path limits |
| TLS/SSL | Automatic certificate management |

## CDN Monitoring

| Metric | What It Reveals |
|--------|-----------------|
| Cache hit ratio | How effective caching is (target > 90%) |
| Origin latency | How fast origin responds |
| Edge latency | How fast edge serves |
| Bandwidth usage | Traffic volume and cost |
| Error rate (4xx, 5xx) | Client and server issues |

**Links**: [[Caching Strategies]] | [[DNS Deep Dive]] | [[Cloud Computing]] | [[Web Security]] | [[HTTP Protocol]]
