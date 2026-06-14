---
id: a1b2c3d4-1099-4000-8000-000000000099
title: Helm
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001099
---

**Links**: [[Kubernetes Basics]] | [[Terraform]] | [[Infrastructure as Code]] | [[Cloud Computing]] | [[Vagrant]] | [[Ansible]]


# Helm

Helm is the package manager for Kubernetes. Charts bundle YAML manifests into reusable, configurable packages with versioning and dependency management.

## Core Concepts

| Concept | Description |
|---------|-------------|
| Chart | Package of Kubernetes manifests |
| Release | A deployed instance of a chart |
| Repository | Chart collection (like apt/yum) |
| Values | Configuration overrides for a chart |
| Template | Go-templated Kubernetes manifest |
| Hook | Chart lifecycle actions (pre/post install) |

## Chart Structure

```
mychart/
├── Chart.yaml          # Metadata (name, version, deps)
├── values.yaml         # Default configuration values
├── charts/             # Subchart dependencies
├── templates/          # Kubernetes manifest templates
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── _helpers.tpl   # Named templates
│   └── tests/          # Test pods
└── crds/               # Custom Resource Definitions
```

## Chart.yaml

```yaml
apiVersion: v2
name: myapp
description: A web application
version: 1.2.3
appVersion: "1.0.0"
type: application

dependencies:
  - name: postgresql
    version: "~12.0"
    repository: https://charts.bitnami.com/bitnami
    condition: postgresql.enabled
  - name: redis
    version: "~17.0"
    repository: https://charts.bitnami.com/bitnami
```

## Template Example

```yaml
# templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app: {{ .Values.app.name }}
    version: {{ .Values.app.tag | default .Chart.AppVersion }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Values.app.name }}
  template:
    metadata:
      labels:
        app: {{ .Values.app.name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: {{ .Values.service.port }}
          env:
            {{- toYaml .Values.env | nindent 12 }}
          resources:
            {{- toYaml .Values.resources | nindent 12 }}
```

## Values

```yaml
# values.yaml
replicaCount: 3

image:
  repository: nginx
  tag: "1.25"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 80

env:
  - name: NODE_ENV
    value: production

resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 500m
    memory: 512Mi

ingress:
  enabled: true
  host: myapp.example.com
```

## Commands

```bash
# Install/upgrade/uninstall
helm install myapp ./mychart
helm upgrade myapp ./mychart --set image.tag=1.2.0
helm rollback myapp 2
helm uninstall myapp

# Release management
helm list
helm history myapp
helm get values myapp
helm get manifest myapp

# Repository management
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm search repo nginx
```

## Built-in Objects

| Object | Description |
|--------|-------------|
| `.Values` | Values from values.yaml and --set |
| `.Release` | Release metadata (name, namespace, revision) |
| `.Chart` | Chart.yaml contents |
| `.Files` | Access files in the chart |
| `.Capabilities` | K8s cluster capabilities |
| `.Template` | Template info (name, base path) |

## Template Functions

```yaml
# Default values
replicas: {{ .Values.replicaCount | default 1 }}

# String operations
name: {{ .Values.name | upper | quote }}

# Conditionals
{{- if .Values.ingress.enabled }}
apiVersion: networking.k8s.io/v1
kind: Ingress
{{- end }}

# Loops
{{- range .Values.env }}
- name: {{ .name }}
  value: {{ .value | quote }}
{{- end }}

# Named templates (from _helpers.tpl)
name: {{ include "mychart.fullname" . }}
```

## Testing

```bash
# Lint and validate
helm lint ./mychart
helm template ./mychart  # render locally
helm install --dry-run --debug ./mychart

# Test release (runs test pods)
helm test myapp
```

## Best Practices

- Pin chart dependencies to specific versions
- Use `helm lint` and `helm template` in CI
- Separate environment-specific values files (`values-staging.yaml`, `values-prod.yaml`)
- Keep `values.yaml` with sensible defaults
- Use `_helpers.tpl` for reusable template logic
- Follow the Helm conventions guide (charts.helm.sh)

**Links**: [[Kubernetes Basics]] | [[Kubernetes Deployments]] | [[Docker Containers]] | [[CI CD Pipelines]] | [[Terraform]]
