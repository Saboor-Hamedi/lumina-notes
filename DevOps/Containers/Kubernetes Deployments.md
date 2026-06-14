---
id: dev-054-0000-0000-0000-000000000038
title: Kubernetes Deployments
language: markdown
tags: [devops, containers, kubernetes, deployments]
selection: null
isPinned: false
timestamp: 1781800000016
---
# Kubernetes Deployments

**Links**: [[Kubernetes Basics]] | [[Docker Containers]] | [[CI CD Pipelines]] | [[Infrastructure as Code]] | [[Monitoring and Observability]]

## Rolling Updates

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
```

```bash
kubectl set image deployment/web-app app=myapp:2.0.0
kubectl rollout undo deployment/web-app
kubectl rollout status deployment/web-app
```

## Scaling

```bash
kubectl scale deployment/web-app --replicas=10
kubectl autoscale deployment/web-app --min=3 --max=20 --cpu-percent=70
```

## Health Checks

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 5
readinessProbe:
  httpGet:
    path: /readyz
    port: 8080
  initialDelaySeconds: 3
```

## Resources

```yaml
resources:
  requests:
    memory: "256Mi"
    cpu: "250m"
  limits:
    memory: "512Mi"
    cpu: "500m"
```

**Next**: [[Nginx Configuration]] — Reverse proxy