---
id: a1b2c3d4-1161-4000-8000-000000000161
title: Zero Trust Architecture
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001161
---

**Links**: [[Kubernetes Security]] | [[WireGuard and VPN Technologies]] | [[OAuth and Authentication Protocols]] | [[Vault and Secret Management]] | [[Web Security]] | [[Threat Modeling]]


# Zero Trust Architecture (ZTA)

Zero Trust is a security model that assumes no entity is trusted by default — whether inside or outside the network perimeter. Every request must be authenticated, authorized, and continuously validated.

## Core Principles

| Principle | Description |
|-----------|-------------|
| Verify explicitly | Always authenticate and authorize based on all available data |
| Least privilege access | Grant minimal access needed for task |
| Assume breach | Design as if the network is already compromised |
| Never trust, always verify | No implicit trust based on network location |

## Traditional vs Zero Trust

```
Traditional (castle-and-moat):
  ┌──────────────────────┐
  │  Trusted Internal     │     ┌──────────────┐
  │  Network              │─────│   Internet    │
  │  ┌────┐  ┌────┐     │     │   (untrusted)  │
  │  │App1│  │App2│     │     └──────────────┘
  │  └────┘  └────┘     │         │
  │  All traffic trusted  │     ┌───────┐
  └──────────────────────┘     │  Firewall  │
                                └───────┘

Zero Trust:
  ┌──────────────────┐
  │  ┌────┐  ┌────┐  │
  │  │App1│  │App2│  │  ──── Each request: authenticate + authorize + encrypt
  │  └────┘  └────┘  │
  │  Micro-segments    │
  └──────────────────┘
       │        │
       │  Verified │
       ▼  every    ▼
    Request    Request
```

## Zero Trust Architecture Components

| Component | Function |
|-----------|----------|
| Policy Engine (PE) | Makes access decisions |
| Policy Administrator (PA) | Generates credentials, sessions |
| Policy Enforcement Point (PEP) | Enforces access (gateway) |
| Identity Provider (IdP) | User authentication |
| Data Access Policies | Rules for resource access |
| SIEM/SOAR | Logging and incident response |

## Deployment Models

### Service-Initiated (ZTNaaS)

```
User → PEP (Gateway) → Verify → PEP → Application
         │
         ├── ↔ IdP (authenticate)
         ├── ↔ Policy Engine (authorize)
         └── ↔ Device Posture Check
```

### Device-Initiated (Agent)

```
User → Agent → Verify ──→ Application
         │        │
         │        └── Policy Engine
         │
         └── Device posture, identity, context
```

## Micro-segmentation

Divide the network into isolated segments with granular policies:

```yaml
# Instead of: allow all HTTP traffic on port 80
# Use: allow specific identity + device + app to access specific resource

policies:
  - source:
      identity: engineering-team
      device: corporate-managed
      location: office-vpn
    destination:
      resource: /api/internal/code-repo
      method: [GET, POST]
    conditions:
      time: business-hours
      posture: antivirus-running, disk-encrypted
```

## Google BeyondCorp Model

Access based on user identity + device state, not network location:

```
Access levels:
  - Trusted: corporate-managed device + verified user
  - Untrusted: personal device or unknown user

User connects from coffee shop (untrusted network):
  → Authenticate via IdP
  → Device posture check
  → Access policy evaluates user + device + resource sensitivity
  → Grant limited access to approved applications only
```

## Key Technologies

| Technology | Role in Zero Trust |
|------------|-------------------|
| IAM/SSO | Identity and authentication |
| MFA | Additional verification |
| Device Management (MDM) | Device posture assessment |
| SASE | Converged network + security |
| CASB | Cloud application access |
| DLP | Data loss prevention |
| SIEM | Continuous monitoring |
| EDR | Endpoint detection |

## Implementation Approach

### Phase 1: Identify

```
Catalog:
  - Users and identities
  - Devices (managed, unmanaged, IoT)
  - Applications and APIs
  - Data classification
  - Network flows
```

### Phase 2: Define Policies

```yaml
# Example access policy
resource: "ERP System"
access_rules:
  - allow_if:
      identity:
        department: finance
        role: [accountant, auditor, manager]
      device:
        managed: true
        os: [Windows 11, macOS 14+]
        posture: [antivirus_enabled, disk_encrypted, patch_level >= 2026-05]
      context:
        location: [office, vpn]
        time: "09:00-18:00"
        risk_score: < 50
    deny_if:
      - country: [sanctioned_countries]
      - tor_exit_node: true
```

### Phase 3: Enforce

```
User request → PEP (gateway/agent)
                  │
                  ├── Policy Engine evaluates
                  ├── Risk score calculated
                  ├── MFA challenge (if high risk)
                  └── Access granted/denied
```

### Phase 4: Monitor

```yaml
Continuous monitoring:
  - Failed authentication attempts
  - Anomalous access patterns
  - Privilege escalation
  - Data exfiltration attempts
```

## Common Challenges

| Challenge | Mitigation |
|-----------|------------|
| Legacy systems without modern auth | Use reverse proxy with auth |
| Performance overhead | Edge enforcement, caching |
| User experience friction | Single sign-on, contextual MFA |
| Device management coverage | Agentless posture checks |
| Cost of implementation | Phased rollout, prioritize critical assets |
