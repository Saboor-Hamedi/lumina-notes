---
id: a1b2c3d4-1187-4000-8000-000000000187
title: Threat Modeling
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001187
---
# Threat Modeling

Threat modeling is a structured approach to identifying, documenting, and mitigating security threats in software systems.

## STRIDE Framework

| Category | Definition | Example |
|----------|------------|---------|
| Spoofing | Impersonating someone | Fake login page |
| Tampering | Modifying data/code | SQL injection |
| Repudiation | Denying actions without proof | Missing audit logs |
| Information Disclosure | Exposing confidential data | Data breach |
| Denial of Service | Disrupting service | DDoS attack |
| Elevation of Privilege | Gaining unauthorized access | Privilege escalation |

## PASTA Framework

| Stage | Activity |
|-------|----------|
| 1 | Define business objectives |
| 2 | Define technical scope |
| 3 | Decompose application |
| 4 | Threat analysis |
| 5 | Vulnerability analysis |
| 6 | Risk assessment |
| 7 | Countermeasure development |

## Data Flow Diagram

```
[User] ──HTTPS──► [API Gateway] ──HTTP──► [Auth Service] ──SQL──► [PostgreSQL]
                           │                        │
                           │                        └──TCP──► [Redis]
                           │
                           └──HTTPS──► [Google OAuth]
```

## Threat Identification (STRIDE per Element)

| Element | Threat | STRIDE |
|---------|--------|--------|
| Browser → Gateway | Man-in-the-middle | S, I |
| Service → DB | SQL injection | T, I |
| Auth → Redis | Session hijacking | S, E |
| Logging | PII in logs | I |

## Risk Rating

```yaml
risk: SQL injection in login
likelihood: high
impact: critical
risk_score: 8  # likelihood × impact
mitigation: parameterized queries, WAF, input validation

risk: Token leakage in logs
likelihood: medium
impact: high
risk_score: 4.5
mitigation: structured logging, PII redaction
```

## Mitigations

| ID | Threat | Control | Verification |
|----|--------|---------|-------------|
| M1 | SQL Injection | Parameterized queries | SAST, DAST |
| M2 | Session hijacking | HttpOnly cookies, rotation | Pen test |
| M3 | MITM services | mTLS | Network scan |
| M4 | Token leakage | PKCE, log redaction | Log review |

## Tools

| Tool | Type | Features |
|------|------|----------|
| Microsoft TMT | Desktop | STRIDE, DFD generation |
| OWASP Threat Dragon | Web/Desktop | STRIDE, LINDDUN |
| IriusRisk | SaaS | Risk-based, CI/CD integration |
| PyTM | Code | Python-based modeling |

## Checklist

```
□ Data flow diagram created
□ Trust boundaries identified
□ STRIDE applied to each element
□ Risks rated (likelihood × impact)
□ Mitigations documented
□ Mitigations verified
□ Reviewed by team
□ Updated after significant changes
```
