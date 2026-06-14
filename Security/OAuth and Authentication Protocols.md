---
id: a1b2c3d4-1010-4000-8000-000000000010
title: OAuth and Authentication Protocols
language: markdown
tags: [security, oauth, authentication]
selection: null
isPinned: false
timestamp: 1781500001010
---

**Links**: [[OAuth 2.0 in Practice]] | [[SAML and Enterprise SSO]] | [[JSON Web Tokens]] | [[Web Security]] | [[TLS 1.3 Deep Dive]] | [[Zero Trust Architecture]]


# OAuth and Authentication Protocols

Authentication verifies identity (who you are). Authorization grants permissions (what you can do). Modern web uses multiple protocols for these.

## Authentication vs Authorization

| Action | Authentication | Authorization |
|--------|---------------|--------------|
| Question | Who are you? | What can you do? |
| Protocol | OpenID Connect | OAuth 2.0 |
| Evidence | Password, MFA, SSO | Token, scope |
| Result | ID token | Access token |

## OAuth 2.0 Flows

| Flow | Use Case |
|------|----------|
| Authorization Code | Server-side web apps (most secure) |
| PKCE | Mobile/native apps (no client secret) |
| Client Credentials | Machine-to-machine (no user) |
| Implicit (deprecated) | Browser apps (use PKCE instead) |
| Resource Owner Password | Legacy/trusted apps (avoid) |

### Authorization Code Flow

```
User → Browser → App → Auth Server (login)
   ↓                                      ↓
User approves scope                Auth Server returns code
   ↓                                      ↓
App exchanges code + secret → Access Token + Refresh Token
```

## OpenID Connect (OIDC)

Adds identity layer on top of OAuth 2.0:

- **ID Token**: JWT containing user identity claims
- **UserInfo Endpoint**: Fetch additional profile data
- **Standard scopes**: `openid`, `profile`, `email`, `address`, `phone`

## JWT Structure

```
header.payload.signature

header: {"alg": "RS256", "typ": "JWT"}
payload: {"sub": "user123", "iat": 1516239022, "exp": 1516242622}
signature: HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
```

## Multi-Factor Authentication (MFA)

| Factor | Example |
|--------|---------|
| Something you know | Password |
| Something you have | Phone, hardware key |
| Something you are | Fingerprint, face |

## SSO Protocols

| Protocol | How It Works |
|----------|-------------|
| SAML | XML-based, enterprise, IdP-initiated |
| OIDC | JWT-based, modern, consumer-friendly |
| CAS | Central login, redirect back with ticket |

## Security Best Practices

- Use HTTPS everywhere
- Short token lifetimes (15-60 min access tokens)
- Use refresh tokens for long-lived sessions
- Validate all tokens on every request
- Store tokens securely (never in localStorage)
- Implement rate limiting on auth endpoints
- Use PKCE for all public clients

**Links**: [[JSON Web Tokens]] | [[Web Security]] | [[REST API Design]] | [[API Security]] | [[Database Security]]
