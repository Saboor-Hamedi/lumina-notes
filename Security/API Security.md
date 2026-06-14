---
id: a1b2c3d4-1017-4000-8000-000000000017
title: API Security
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001017
---

**Links**: [[OWASP Top 10]] | [[Web Security]] | [[Secure Coding Practices]] | [[Threat Modeling]] | [[OAuth and Authentication Protocols]] | [[JSON Web Tokens]]


# API Security

API security protects endpoints from unauthorized access, data breaches, injection attacks, and abuse.

## Authentication & Authorization

| Layer | Mechanism | Purpose |
|-------|-----------|---------|
| Transport | HTTPS, TLS 1.3 | Encrypt in transit |
| Identity | API keys, JWT, OAuth 2.0 | Who is calling? |
| Permissions | RBAC, ABAC, scopes | What can they do? |
| Rate Limit | Token bucket, leaky bucket | Prevent abuse |

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

## Input Validation

```python
# Before: no validation
def create_user(name, email):
    db.execute("INSERT INTO users VALUES (%s, %s)", name, email)

# After: validate + sanitize
def create_user(name, email):
    if not re.match(r'^[\w\s]{1,100}$', name):
        raise ValueError("Invalid name")
    if not re.match(r'^[^@]+@[^@]+\.[^@]+$', email):
        raise ValueError("Invalid email")
    db.execute("INSERT INTO users VALUES (%s, %s)", name, email)
```

## Rate Limiting Strategies

| Strategy | How | Used For |
|----------|-----|----------|
| Fixed Window | X requests per minute | Simple throttling |
| Sliding Window | Count requests over rolling window | More accurate |
| Token Bucket | X tokens, refill at Y rate | Bursty traffic |
| Leaky Bucket | Process at fixed rate, queue overflow | Smooth traffic |

## Response Security Headers

```
Strict-Transport-Security: max-age=31536000
Content-Security-Policy: default-src 'self'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
```

## CORS Configuration

```json
{
  "Access-Control-Allow-Origin": "https://trusted-site.com",
  "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE",
  "Access-Control-Allow-Headers": "Authorization, Content-Type",
  "Access-Control-Max-Age": 3600
}
```

## Best Practices

- Use an API gateway (Kong, AWS API Gateway, Envoy)
- Log all authentication failures and monitor for patterns
- Implement request validation at the gateway
- Use short-lived access tokens (15-60 min)
- Never expose internal IPs, error traces, or schema details
- Pin TLS certificates and rotate regularly
- Conduct regular pentesting and dependency scans

**Links**: [[Web Security]] | [[OAuth and Authentication Protocols]] | [[JSON Web Tokens]] | [[Rate Limiting]] | [[REST API Design]]
