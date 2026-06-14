---
id: a1b2c3d4-1160-4000-8000-000000000160
title: Kubernetes Security
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001160
---

**Links**: [[Zero Trust Architecture]] | [[OAuth and Authentication Protocols]] | [[Vault and Secret Management]] | [[API Security]] | [[TLS 1.3 Deep Dive]] | [[Web Security]]


# Kubernetes Security

Kubernetes security encompasses cluster-level controls, workload security, network policies, authentication, authorization, and secret management.

## Attack Surface

```
API Server
    │
    ├── etcd (encrypted at rest?)
    ├── Kubelets (authentication?)
    ├── kube-controller-manager
    ├── kube-scheduler
    └── CoreDNS

Workloads:
    ├── Pod-to-pod communication (network policies?)
    ├── Container runtime (rootless?)
    └── Image vulnerabilities (scanning?)
```

## Authentication

```yaml
# User types:
# - X.509 certificates
# - Service accounts (for pods)
# - Static token file
# - Webhook token (OIDC, etc.)

# Service account with limited scope
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-app
  namespace: default
automountServiceAccountToken: false  # Disable if app doesn't need API access
---
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: my-app-token
  annotations:
    kubernetes.io/service-account.name: my-app
```

## Authorization (RBAC)

```yaml
# Role: permissions within a namespace
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
  - apiGroups: [""]
    resources: ["pods", "pods/log"]
    verbs: ["get", "list", "watch"]
  - apiGroups: ["apps"]
    resources: ["deployments"]
    verbs: ["get", "list", "watch"]
---
# ClusterRole: permissions cluster-wide
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: namespace-reader
rules:
  - apiGroups: [""]
    resources: ["namespaces"]
    verbs: ["get", "list"]
---
# RoleBinding: bind Role to user/service account
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
  - kind: ServiceAccount
    name: my-app
    namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

## Pod Security Standards

```yaml
# Pod Security Admission (replaces PodSecurityPolicy)
# Three profiles: privileged, baseline, restricted

apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    pod-security.kubernetes.io/enforce: restricted
    pod-security.kubernetes.io/enforce-version: latest
    pod-security.kubernetes.io/audit: baseline
    pod-security.kubernetes.io/warn: baseline
---
# Restricted pod example
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
    seccompProfile:
      type: RuntimeDefault
  containers:
    - name: app
      image: myapp:latest
      securityContext:
        allowPrivilegeEscalation: false
        capabilities:
          drop: ["ALL"]
        readOnlyRootFilesystem: true
        runAsNonRoot: true
```

## Network Policies

```yaml
# Default: deny all ingress, allow all egress

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: api-network-policy
spec:
  podSelector:
    matchLabels:
      app: api-service
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend    # Allow frontend to reach API
        - namespaceSelector:
            matchLabels:
              name: monitoring  # Allow Prometheus from monitoring NS
      ports:
        - port: 8080
          protocol: TCP
  egress:
    - to:
        - podSelector:
            matchLabels:
              app: database   # Allow API to talk to database
        - ipBlock:
            cidr: 0.0.0.0/0  # Allow external DNS queries
            except:
              - 10.0.0.0/8   # Block internal cloud metadata
      ports:
        - port: 5432
          protocol: TCP
        - port: 53
          protocol: UDP
```

## Secrets Management

```yaml
# Built-in secrets (base64 encoded — not encrypted)
apiVersion: v1
kind: Secret
metadata:
  name: db-credentials
type: Opaque
data:
  username: ZGJfdXNlcg==    # echo -n 'db_user' | base64
  password: c0VjcjN0IQ==
---
# External Secrets Operator (recommended)
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: aws-secret
spec:
  refreshInterval: 1h
  secretStoreRef:
    name: aws-secretsmanager
    kind: ClusterSecretStore
  target:
    name: db-credentials
    creationPolicy: Owner
  data:
    - secretKey: username
      remoteRef:
        key: /production/database/credentials
        property: username
---
# Pod using secrets
apiVersion: v1
kind: Pod
spec:
  containers:
    - name: app
      env:
        - name: DB_USERNAME
          valueFrom:
            secretKeyRef:
              name: db-credentials
              key: username
```

## Container Image Security

```yaml
# Image policy webhook: prevent untrusted images
apiVersion: v1
kind: Pod
spec:
  containers:
    - name: app
      image: myregistry.io/app:1.2.3@sha256:abc123...  # Immutable digest

---
# Admission controller: validate image signatures
# Use tools: Cosign (Sigstore), Notary

# Deploy with signed images:
COSIGN_EXPERIMENTAL=1 cosign sign myregistry.io/app:1.2.3
```

## Runtime Security

| Tool | Category | Description |
|------|----------|-------------|
| Falco | Runtime | Detect anomalous behavior |
| AppArmor | LSM | Mandatory access control |
| Seccomp | Syscall | Restrict system calls |
| SELinux | LSM | Label-based MAC |
| gVisor | Sandbox | Userspace kernel |
| Kata Containers | VM isolation | Lightweight VM per pod |

## API Server Hardening

```yaml
# kube-apiserver flags
--enable-admission-plugins=NodeRestriction,PodSecurity,NamespaceLifecycle
--authorization-mode=Node,RBAC
--anonymous-auth=false
--profiling=false
--audit-log-path=/var/log/kubernetes/audit.log
--audit-log-maxbackup=10
--audit-log-maxsize=100
--encryption-provider-config=/etc/kubernetes/encryption-config.yaml

# etcd encryption
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources: ["secrets"]
    providers:
      - aescbc:
          keys:
            - name: key1
              secret: c2VjcmV0IGlzIHNlY3VyZQ==
      - identity: {}  # Fallback
```

## Audit Logging

```yaml
# Audit policy levels: None, Metadata, Request, RequestResponse
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
  - level: RequestResponse
    resources:
      - group: ""
        resources: ["secrets", "configmaps"]
  - level: Metadata
    # Log all mutating requests
    verbs: ["create", "update", "delete", "patch"]
  - level: None
    # Skip health/status endpoints
    resources:
      - group: ""
        resources: ["healthz", "livez", "readyz"]
```
