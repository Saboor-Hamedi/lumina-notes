---
id: a1b2c3d4-1017-4000-8000-000000000017
title: API Security
language: markdown
tags: [security, api-security]
selection: null
isPinned: false
timestamp: 1781500001017
---
**Links**: [[OWASP Top 10]] | [[Web Security]] | [[Secure Coding Practices]] | [[Threat Modeling]] | [[OAuth and Authentication Protocols]] | [[JSON Web Tokens]]
# API Security

API security protects endpoints from unauthorized access, data breaches, injection attacks, and abuse.

## OWASP API Security Top 10

| Rank | Category | Description |
|------|----------|-------------|
| API1 | Broken Object Level Authorization | Insecure direct object references (IDOR) |
| API2 | Broken Authentication | Weak auth, session hijacking, credential stuffing |
| API3 | Broken Object Property Level Assignment | Mass assignment, excessive data exposure |
| API4 | Unrestricted Resource Consumption | No rate limiting, large payloads, DDoS |
| API5 | Broken Function Level Authorization | Missing admin checks on privileged endpoints |
| API6 | Unrestricted Access to Sensitive Business Flows | Automated abuse of business logic |
| API7 | Server Side Request Forgery | SSRF to internal networks |
| API8 | Security Misconfiguration | Default creds, verbose errors, missing headers |
| API9 | Improper Inventory Management | Zombie APIs, undocumented endpoints |
| API10 | Unsafe Consumption of APIs | No validation of third-party API responses |

## Authentication & Authorization

| Method | Mechanism | Strength | Best For |
|--------|-----------|----------|----------|
| API Keys | Static string in header | Low | Internal services, simple identification |
| JWT | Signed token with claims | Medium | Stateless API auth, microservices |
| OAuth 2.0 | Delegated authorization framework | High | Third-party access, mobile apps |
| mTLS | Mutual TLS with client certs | Very High | Service-to-service, zero-trust networks |
| Session Cookies | Server-stored session ID | Medium | Server-rendered web apps |

## Common API Attacks

| Attack | Description | Mitigation |
|--------|-------------|------------|
| Injection | SQL, NoSQL, command injection | Parameterized queries, input validation |
| Broken Auth | Weak password reset, session hijacking | MFA, short-lived tokens |
| Excessive Data | Returning full objects instead of needed fields | Field selection, DTOs |
| Mass Assignment | Overwriting protected fields | Whitelist allowed fields |
| IDOR | Accessing another user's resource | Ownership checks on every request |
| SSRF | Server makes requests to internal network | Block private IPs, validate URLs |
| CSRF | Cross-site request forgery | CSRF tokens, SameSite cookies |

## Rate Limiting Strategies

| Strategy | Mechanism | Behavior | Use Case |
|----------|-----------|----------|----------|
| Fixed Window | Counter resets each window | Bursts at boundary | Simple throttling per IP |
| Sliding Window | Rolling time window counter | Even distribution | Accurate rate tracking |
| Token Bucket | Bucket of X tokens, refill at Y rate | Allows short bursts | Traffic shaping, API tiers |
| Leaky Bucket | Process at fixed rate, queue overflow | Smooth output | Downstream protection |
| Sliding Log | Timestamp log per request | Most accurate | Critical financial APIs |

## Input Validation & Sanitization

```python
import re
import html
from typing import Optional

def sanitize_input(user_input: str, max_length: int = 1000) -> Optional[str]:
    if not user_input or len(user_input) > max_length:
        return None
    escaped = html.escape(user_input.strip())
    return escaped

def validate_email(email: str) -> bool:
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return bool(re.match(pattern, email))

def validate_uuid(value: str) -> bool:
    pattern = r'^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$'
    return bool(re.match(pattern, value, re.I))
```

## CORS Configuration Best Practices

```json
{
  "Access-Control-Allow-Origin": "https://trusted-site.com",
  "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, PATCH",
  "Access-Control-Allow-Headers": "Authorization, Content-Type",
  "Access-Control-Max-Age": 3600
}
```

Avoid wildcard origins with credentials. Restrict methods and headers to minimum needed. Cache OPTIONS preflight responses.

## Response Security Headers

```
Strict-Transport-Security: max-age=31536000; includeSubDomains
Content-Security-Policy: default-src 'self'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: geolocation=(), microphone=()
```

## Best Practices

- Use an API gateway (Kong, AWS API Gateway, Envoy)
- Log all authentication failures and monitor for patterns
- Implement request validation at the gateway
- Use short-lived access tokens (15-60 min)
- Never expose internal IPs, error traces, or schema details
- Pin TLS certificates and rotate regularly
- Conduct regular pentesting and dependency scans
- Apply least privilege to API keys and service accounts

**Links**: [[Web Security]] | [[OAuth and Authentication Protocols]] | [[JSON Web Tokens]] | [[Rate Limiting]] | [[REST API Design]]
