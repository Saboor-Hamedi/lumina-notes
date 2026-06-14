---
id: dev-053-0000-0000-0000-000000000037
title: Kubernetes Basics
language: markdown
tags: [devops, containers, kubernetes]
selection: null
isPinned: false
timestamp: 1781800000015
---
# Kubernetes Basics

**Links**: [[Docker Containers]] | [[Cloud Computing]] | [[Microservices Architecture]] | [[CI CD Pipelines]] | [[Infrastructure as Code]] | [[Monitoring and Observability]]

## What is Kubernetes?

Kubernetes automates deployment, scaling, and management of containerized applications.

## Architecture

- **Control Plane**: API Server, Scheduler, Controller Manager, etcd
- **Worker Nodes**: kubelet, container runtime, pods

## Core Objects

| Object | Description |
|--------|-------------|
| **Pod** | Smallest deployable unit |
| **Deployment** | Declarative pod management |
| **Service** | Stable network endpoint |
| **ConfigMap** | Non-sensitive config |
| **Secret** | Sensitive data (base64) |
| **Ingress** | External HTTP routing |

## Deployment Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: app
        image: myapp:latest
        ports:
        - containerPort: 8080
```

## kubectl Commands

```bash
kubectl get pods
kubectl logs pod-name
kubectl exec -it pod-name -- bash
kubectl apply -f deployment.yaml
kubectl delete pod pod-name
kubectl port-forward pod 8080:80
```

**Next**: [[Kubernetes Deployments]] — Rolling updates and scaling