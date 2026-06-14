---
id: a1b2c3d4-1103-4000-8000-000000000103
title: Vault and Secret Management
language: markdown
tags: [security, vault, secrets]
selection: null
isPinned: false
timestamp: 1781500001103
---

**Links**: [[Kubernetes Security]] | [[API Security]] | [[Zero Trust Architecture]] | [[Cryptography Basics]] | [[TLS 1.3 Deep Dive]] | [[Threat Modeling]]


# Vault and Secret Management

HashiCorp Vault manages secrets, encryption keys, and access tokens. It provides dynamic secrets, leasing, revocation, and audit logging.

## Core Concepts

| Concept | Description |
|---------|-------------|
| Secret | Any credential (API key, password, cert) |
| Engine | Secret backend (KV, AWS, DB, PKI) |
| Path | Addressable location for secrets |
| Policy | Access control rules (HCL) |
| Lease | TTL for dynamic secrets (auto-renew or expire) |
| Auth Method | How to authenticate to Vault |

## Architecture

```
Application → Vault API → Auth (token/JWT/K8s)
                              ↓
                    ┌─────────────────┐
                    │  Vault Server    │
                    │                  │
                    │  ┌───────────┐  │
                    │  │ Secrets    │  │
                    │  │ Engine     │  │
                    │  │ (KV, AWS,  │  │
                    │  │  DB, PKI)  │  │
                    │  └───────────┘  │
                    │                  │
                    │  Storage Backend │
                    │  (Consul, Raft,  │
                    │   S3, filesystem)│
                    └─────────────────┘
```

## Static Secrets

```bash
# Enable KV engine
vault secrets enable -path=secret kv-v2

# Write a secret
vault kv put secret/myapp/database \
    username=db_user \
    password=s3cr3t

# Read a secret
vault kv get secret/myapp/database

# Delete versions
vault kv delete secret/myapp/database
```

## Dynamic Secrets

Secrets generated on-demand with automatic expiry:

```bash
# Enable database engine
vault secrets enable database

# Configure PostgreSQL
vault write database/config/postgres \
    plugin_name=postgresql-database-plugin \
    allowed_roles="myapp-role" \
    connection_url="postgresql://{{username}}:{{password}}@localhost:5432/mydb"

# Create role (creates user with TTL)
vault write database/roles/myapp-role \
    db_name=postgres \
    creation_statements="CREATE USER \"{{name}}\" WITH PASSWORD '{{password}}' VALID UNTIL '{{expiration}}'; GRANT SELECT ON ALL TABLES IN SCHEMA public TO \"{{name}}\";" \
    default_ttl="1h" \
    max_ttl="24h"

# Get dynamic credential
vault read database/creds/myapp-role

# Output:
# Key                Value
# lease_id           postgres/creds/myapp-role/abc123
# lease_duration     1h
# password           aB3x... (auto-generated)
# username           v-token-myapp-role-xyz
```

## PKI Engine

```bash
# Generate TLS certificates on-demand
vault secrets enable pki

# Configure root CA
vault write pki/root/generate/internal common_name=example.com ttl=87600h

# Issue certificate
vault write pki/issue/myapp \
    common_name=myapp.example.com \
    ttl=72h \
    alt_names="myapp-staging.example.com"
```

## Auth Methods

| Method | Use Case |
|--------|----------|
| Token | Dev/test, Vault admin |
| AppRole | Machine-to-machine auth |
| Kubernetes | Pod identity via service account |
| JWT/OIDC | SSO, identity providers |
| LDAP | Enterprise user auth |
| AWS IAM | EC2 instance identity |

### Kubernetes Auth

```bash
vault auth enable kubernetes

vault write auth/kubernetes/config \
    kubernetes_host=https://kubernetes.default.svc

vault write auth/kubernetes/role/myapp \
    bound_service_account_names=vault-auth \
    bound_service_account_namespaces=default \
    policies=myapp-policy \
    ttl=1h
```

```yaml
# Pod annotation
metadata:
  annotations:
    vault.hashicorp.com/agent-inject: "true"
    vault.hashicorp.com/role: "myapp"
    vault.hashicorp.com/agent-inject-secret-database: "database/creds/myapp-role"
```

## Policies

```hcl
path "secret/data/myapp/*" {
  capabilities = ["read", "list"]
}

path "database/creds/myapp-role" {
  capabilities = ["read"]
}

path "sys/lease/renew" {
  capabilities = ["update"]
}
```

## Best Practices

- Use dynamic secrets with short TTLs
- Never commit secrets to version control
- Use Vault Agent sidecar for automatic secret injection
- Enable audit logging to detect unauthorized access
- Use response wrapping for one-time secret delivery
- Seal/unseal Vault on restart (distribute unseal keys)

**Links**: [[Kubernetes Basics]] | [[API Security]] | [[Environment Variables]] | [[Consul]] | [[Database Security]]
