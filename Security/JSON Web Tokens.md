---
id: dev-061-0000-0000-0000-000000000047
title: JSON Web Tokens
language: markdown
tags: [security, jwt, authentication]
selection: null
isPinned: false
timestamp: 1781800000025
---
# JSON Web Tokens

**Links**: [[OAuth and Authentication Protocols]] | [[OAuth 2.0 in Practice]] | [[Web Security]] | [[REST API Design]] | [[API Versioning]] | [[HTTP Protocol]] | [[API Security]] | [[PostgreSQL Features]]

**See also**: [[Secure Coding Practices]], [[TLS 1.3 Deep Dive]], [[SAML and Enterprise SSO]]

## What is JWT?

JWT is a compact, URL-safe token format for securely transmitting claims between parties. It's commonly used for API authentication.

## Structure

A JWT has three parts separated by dots:

```
header.payload.signature

eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jM
```

### Header

```json
{"alg": "HS256", "typ": "JWT"}
```

### Payload (Claims)

```json
{
  "sub": "user_123",
  "name": "Alice",
  "role": "admin",
  "iat": 1516239022,
  "exp": 1516242622
}
```

| Claim | Meaning |
|-------|---------|
| `sub` | Subject (user ID) |
| `iat` | Issued at (timestamp) |
| `exp` | Expiration (timestamp) |
| `iss` | Issuer |
| `aud` | Audience |

### Signature

```
HMACSHA256(
  base64url(header) + "." + base64url(payload),
  secret_key
)
```

## Python Implementation

```python
import jwt
from datetime import datetime, timedelta

SECRET = "your-secret-key"

def create_token(user_id: str) -> str:
    payload = {
        "sub": user_id,
        "iat": datetime.utcnow(),
        "exp": datetime.utcnow() + timedelta(hours=24),
    }
    return jwt.encode(payload, SECRET, algorithm="HS256")

def verify_token(token: str) -> dict:
    try:
        return jwt.decode(token, SECRET, algorithms=["HS256"])
    except jwt.ExpiredSignatureError:
        raise Exception("Token expired")
    except jwt.InvalidTokenError:
        raise Exception("Invalid token")
```

## Best Practices

- Use short expiration (15-60 minutes)
- Use refresh tokens for longer sessions
- Store tokens securely (HttpOnly cookies, not localStorage)
- Always use HTTPS
- Rotate signing keys regularly
- Use RS256 (asymmetric) for multi-service systems

**Next**: [[HTTP Caching]] — Cache control