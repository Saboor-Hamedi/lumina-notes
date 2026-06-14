---
id: a1b2c3d4-1135-4000-8000-000000000135
title: Secure Coding Practices
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001135
---

**Links**: [[OWASP Top 10]] | [[Web Security]] | [[API Security]] | [[Threat Modeling]] | [[Cryptography Basics]] | [[Vault and Secret Management]]


# Secure Coding Practices

Secure coding prevents vulnerabilities at the source. These practices apply across all languages and frameworks.

## Input Validation

```javascript
// Validate type, length, format
function createUser(req) {
    const { name, email, age } = req.body;

    if (typeof name !== "string" || name.length > 100) throw new Error("Invalid name");
    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) throw new Error("Invalid email");
    if (typeof age !== "number" || age < 0 || age > 150) throw new Error("Invalid age");

    return db.users.create({ name: name.trim(), email: email.toLowerCase(), age });
}

// Use validation libraries
import { z } from "zod";
const schema = z.object({
    name: z.string().max(100),
    email: z.string().email(),
    age: z.number().int().min(0).max(150)
});
schema.parse(req.body);
```

## Output Encoding

```javascript
// Prevent XSS — encode output based on context
// HTML context:
function escapeHtml(str) {
    return str
        .replace(/&/g, "&amp;")
        .replace(/</g, "&lt;")
        .replace(/>/g, "&gt;")
        .replace(/"/g, "&quot;")
        .replace(/'/g, "&#x27;");
}

// JavaScript context: JSON.stringify (not just replace)
// URL context: encodeURIComponent(str)
// CSS context: hex encoding
```

## Authentication

```python
import bcrypt
import secrets
from datetime import datetime, timedelta

# Password hashing
def hash_password(password: str) -> str:
    return bcrypt.hashpw(password.encode(), bcrypt.gensalt(rounds=12))

def verify_password(password: str, hash: str) -> bool:
    return bcrypt.checkpw(password.encode(), hash.encode())

# Session tokens
def create_session(user_id: str) -> str:
    token = secrets.token_urlsafe(32)
    redis.setex(f"session:{token}", 3600, user_id)
    return token
```

## Authorization

```javascript
// Role-based access control (RBAC)
const ROLES = { ADMIN: "admin", EDITOR: "editor", VIEWER: "viewer" };

const PERMISSIONS = {
    "create:post": [ROLES.ADMIN, ROLES.EDITOR],
    "delete:post": [ROLES.ADMIN],
    "view:draft": [ROLES.ADMIN, ROLES.EDITOR],
};

function requirePermission(action) {
    return (req, res, next) => {
        const userRole = req.user.role;
        if (!PERMISSIONS[action]?.includes(userRole)) {
            return res.status(403).json({ error: "Insufficient permissions" });
        }
        next();
    };
}
```

## Error Handling

```javascript
// Never leak stack traces or internal details
app.use((err, req, res, next) => {
    logger.error(err.stack);                    // Log full details

    res.status(err.status || 500).json({        // Return minimal info
        error: err.status === 500
            ? "Internal server error"
            : err.message
    });
});
```

## Data Storage

| Data Type | Storage Method |
|-----------|---------------|
| Passwords | bcrypt/argon2 hash |
| Credit cards | DON'T STORE (use tokenization) |
| PII | Encrypted at rest (AES-256-GCM) |
| Session tokens | Hashed in database |
| API keys | Hashed + prefix for lookup |

```javascript
// Encrypt sensitive data
const crypto = require("crypto");

function encrypt(text, key) {
    const iv = crypto.randomBytes(16);
    const cipher = crypto.createCipheriv("aes-256-gcm", key, iv);
    let encrypted = cipher.update(text, "utf8", "hex");
    encrypted += cipher.final("hex");
    return { iv: iv.toString("hex"), encrypted, tag: cipher.getAuthTag().toString("hex") };
}
```

## Logging (No Secrets)

```javascript
// NEVER log:
//   - Passwords, tokens, secrets
//   - Credit card numbers
//   - Full database queries
//   - Stack traces to the client

// SAFE logging:
logger.info({ userId: req.user.id, action: "login", ip: req.ip });
```

## Dependencies

```bash
# Keep dependencies updated
npm outdated
npm audit
dependabot configure

# Pin versions in production
npm ci --only=production

# Subresource integrity for CDN scripts
<script src="https://cdn.example.com/lib.js"
        integrity="sha384-abc123..."
        crossorigin="anonymous"></script>
```

## Secure Defaults

```yaml
# Security headers (Helmet.js)
app.use(helmet({
    contentSecurityPolicy: { directives: { defaultSrc: ["'self'"] } },
    hsts: { maxAge: 31536000, includeSubDomains: true },
    frameguard: { action: "deny" }
}));
```

## Principle of Least Privilege

```sql
-- Database user permissions
CREATE USER app_user WITH PASSWORD '...';
GRANT SELECT, INSERT, UPDATE ON orders TO app_user;
GRANT USAGE ON SEQUENCE orders_id_seq TO app_user;
-- NO: DROP, TRUNCATE, CREATE, DELETE (unless needed)
```

**Links**: [[OWASP Top 10]] | [[Web Security]] | [[API Security]] | [[OAuth and Authentication Protocols]] | [[Cryptography Basics]]
