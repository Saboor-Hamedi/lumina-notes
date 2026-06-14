---
id: a1b2c3d4-1127-4000-8000-000000000127
title: OAuth 2.0 in Practice
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001127
---

**Links**: [[OAuth and Authentication Protocols]] | [[SAML and Enterprise SSO]] | [[JSON Web Tokens]] | [[Web Security]] | [[API Security]] | [[Secure Coding Practices]]


# OAuth 2.0 in Practice

OAuth 2.0 is the industry-standard protocol for authorization. This note covers real-world implementation patterns, common pitfalls, and deployment considerations.

## OAuth Roles

| Role | Description | Example |
|------|-------------|---------|
| Resource Owner | User who authorizes access | End user |
| Client | Application requesting access | Web app, mobile app |
| Authorization Server | Issues tokens | Auth0, Keycloak, Google |
| Resource Server | Hosts protected resources | API server |

## Common Flows

### Authorization Code + PKCE (Recommended for all clients)

```
1. Client redirects user to auth server
2. Auth server authenticates user, redirects with code
3. Client exchanges code + code_verifier for tokens
4. Client uses access token to call API
```

```javascript
// PKCE code challenge generation
async function generatePKCE() {
    const verifier = generateRandomString(64);
    const challenge = await sha256(verifier);
    return { verifier, challenge: base64url(challenge) };
}

// Authorization URL
const authUrl = new URL("https://auth.example.com/authorize");
authUrl.searchParams.set("response_type", "code");
authUrl.searchParams.set("client_id", CLIENT_ID);
authUrl.searchParams.set("redirect_uri", CALLBACK_URL);
authUrl.searchParams.set("code_challenge", challenge);
authUrl.searchParams.set("code_challenge_method", "S256");
authUrl.searchParams.set("scope", "openid profile email");

// Token exchange
const response = await fetch("https://auth.example.com/token", {
    method: "POST",
    headers: { "Content-Type": "application/x-www-form-urlencoded" },
    body: new URLSearchParams({
        grant_type: "authorization_code",
        code: AUTH_CODE,
        redirect_uri: CALLBACK_URL,
        client_id: CLIENT_ID,
        code_verifier: verifier,
    })
});
const tokens = await response.json();
```

## Token Validation on Resource Server

```python
import jwt
from jwt import PyJWKClient

# JWKS endpoint provides public keys
jwks_client = PyJWKClient("https://auth.example.com/.well-known/jwks.json")

def validate_token(access_token):
    signing_key = jwks_client.get_signing_key_from_jwt(access_token)

    payload = jwt.decode(
        access_token,
        signing_key.key,
        algorithms=["RS256"],
        audience="api://myapp",
        issuer="https://auth.example.com/",
        options={"verify_exp": True}
    )

    # Additional checks
    assert "scope" in payload
    scopes = payload["scope"].split()
    assert "read:users" in scopes

    return payload
```

## Scopes & Permissions

```
Scope format: {resource}:{action}
Examples: read:users, write:orders, admin:all
```

```json
// Access token claims
{
  "iss": "https://auth.example.com",
  "sub": "user_12345",
  "aud": "api://myapp",
  "exp": 1718000000,
  "iat": 1717996400,
  "scope": "read:users write:orders",
  "email": "alice@example.com"
}
```

## Refresh Token Rotation

Rotate refresh tokens on each use to prevent token theft:

```python
# If a stolen refresh token is used, the legitimate user's new token is invalidated
# If both old AND new are used → token theft detected → revoke all tokens
```

## Common Pitfalls

| Pitfall | Mitigation |
|---------|------------|
| Storing tokens in localStorage | Use httpOnly cookies (BFF pattern) |
| Not validating token expiry | Check exp claim on every request |
| Implicit flow (deprecated) | Use Auth Code + PKCE instead |
| Ignoring state parameter | Include anti-CSRF state in auth request |
| Hardcoded client secrets | Client secrets in mobile = not secret |
| Not rotating refresh tokens | Rotate on each use |

## Backend for Frontend (BFF) Pattern

```
Browser ↔ BFF (same origin, httpOnly cookie) ↔ Auth Server
               │
               └── ↔ API with access token
```

Keeps tokens out of the browser (prevents XSS token theft).

## Token Storage Decision

| Storage | XSS Risk | CSRF Risk | Works offline |
|---------|----------|-----------|--------------|
| localStorage | High | None | Yes |
| sessionStorage | High | None | No |
| httpOnly cookie (BFF) | None | Low | Yes |
| In-memory (JS) | None | None | No |

**Links**: [[OAuth and Authentication Protocols]] | [[JSON Web Tokens]] | [[API Security]] | [[Web Security]] | [[SAML and Enterprise SSO]]
