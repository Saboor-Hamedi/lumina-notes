---
id: a1b2c3d4-1100-4000-8000-000000000100
title: Istio Service Mesh
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001100
---
# Istio Service Mesh

Istio is a service mesh that adds observability, traffic management, and security to microservices without changing application code.

## Service Mesh Architecture

```
Pod A                          Pod B
┌──────────┐                  ┌──────────┐
│ Service A │ ◄─── HTTP/gRPC ──► │ Service B │
└─────┬────┘                  └─────┬────┘
      │                            │
┌─────┴────┐                  ┌─────┴────┐
│ Envoy    │                  │ Envoy    │
│ Proxy    │                  │ Proxy    │
└─────┬────┘                  └─────┬────┘
      │                            │
      └──────────┬─────────────────┘
                 │
         ┌───────┴───────┐
         │   Istiod      │
         │ (Control Plane)│
         │ - Pilot       │
         │ - Citadel     │
         │ - Galley      │
         └───────────────┘
```

## Data Plane vs Control Plane

| Plane | Component | Role |
|-------|-----------|------|
| Data | Envoy Proxy (sidecar) | Intercept all traffic, enforce policies |
| Control | Istiod | Configuration, certificate management, service discovery |

## Traffic Management

```yaml
# VirtualService — routing rules
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts:
    - myapp
  http:
    - match:
        - headers:
            version:
              exact: v2
      route:
        - destination:
            host: myapp
            subset: v2
    - route:
        - destination:
            host: myapp
            subset: v1
          weight: 90
        - destination:
            host: myapp
            subset: v2
          weight: 10

---
# DestinationRule — traffic policies
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: myapp
spec:
  host: myapp
  subsets:
    - name: v1
      labels:
        version: v1
    - name: v2
      labels:
        version: v2
  trafficPolicy:
    connectionPool:
      tcp:
        maxConnections: 100
    loadBalancer:
      simple: ROUND_ROBIN
```

## Security

```yaml
# Mutual TLS (mTLS) — automatic
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
  namespace: istio-system
spec:
  mtls:
    mode: STRICT  # STRICT | PERMISSIVE | DISABLE

# Authorization policy
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: require-jwt
spec:
  selector:
    matchLabels:
      app: myapp
  action: ALLOW
  rules:
    - from:
        - source:
            requestPrincipals: ["*"]
      to:
        - operation:
            methods: ["GET", "POST"]
```

## Observability

```yaml
apiVersion: telemetry.istio.io/v1alpha1
kind: Telemetry
metadata:
  name: mesh-default
spec:
  tracing:
    - providers:
        - name: zipkin
      randomSamplingPercentage: 100
```

| Tool | Data |
|------|------|
| Grafana | Request volume, latency, error rate |
| Kiali | Service graph, topology visualization |
| Jaeger/Tempo | Distributed tracing |
| Prometheus | Metrics collection |

## Fault Injection

```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts:
    - myapp
  http:
    - fault:
        delay:
          percentage:
            value: 50
          fixedDelay: 5s
        abort:
          percentage:
            value: 10
          httpStatus: 500
      route:
        - destination:
            host: myapp
```

## Circuit Breaking

Automatically stop sending traffic to unhealthy instances:

- Consecutive error count threshold
- Connection pool limits
- Ejection time
- Max ejection percentage

## Performance Overhead

| Resource | Impact |
|----------|--------|
| Latency | +2-5ms per hop (proxy overhead) |
| CPU | 5-10% per sidecar |
| Memory | 50-100 MB per sidecar |

**Links**: [[Service Mesh]] | [[Kubernetes Basics]] | [[Envoy Proxy]] | [[Kubernetes Deployments]] | [[Microservices Architecture]]
