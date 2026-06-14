---
id: a1b2c3d4-0007-4000-8000-000000000007
title: Web Security
language: markdown
tags: [security, web-security]
selection: null
isPinned: false
timestamp: 1781400000007
---

**Links**: [[OWASP Top 10]] | [[API Security]] | [[Secure Coding Practices]] | [[Threat Modeling]] | [[Cryptography Basics]] | [[OAuth and Authentication Protocols]] | [[Zero Trust Architecture]]


# Web Security

Web security protects applications from attacks, data breaches, and unauthorized access.

## OWASP Top 10 (Common Threats)

| Rank | Threat | Mitigation |
|------|--------|------------|
| 1 | Broken Access Control | Role-based permissions, validate every request |
| 2 | Cryptographic Failures | Use HTTPS, hash passwords (bcrypt/argon2) |
| 3 | Injection (SQL, XSS) | Parameterized queries, input sanitization |
| 4 | Insecure Design | Threat modeling, security review |
| 5 | Security Misconfiguration | Minimal surface, disable debug in prod |
| 6 | Vulnerable Components | Regular dependency updates, SCA tools |
| 7 | Auth Failures | MFA, rate limiting, secure sessions |
| 8 | Data Integrity Flaws | Signatures, checksums |
| 9 | Logging Failures | Centralized logging, alert on anomalies |
| 10 | SSRF | Validate URLs, deny private networks |

## Authentication & Authorization

- **JWT**: Stateless tokens with claims
- **OAuth 2.0**: Delegated authorization flow
- **API Keys**: Simple secret-based auth
- **Session Cookies**: Server-side sessions

## Best Practices

```sql
-- ALWAYS use parameterized queries (prevents SQL injection)
SELECT * FROM users WHERE email = %s
```

- Hash passwords with bcrypt, never store plaintext
- Use Content-Security-Policy headers
- Enable CORS only for trusted origins
- Rate limit API endpoints

**See also**: [[HTTP Protocol]], [[REST API Design]], [[REST API Design]], [[Dev Environment Setup]]