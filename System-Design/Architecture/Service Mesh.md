---
id: new-015-0000-0000-0000-000000000015
title: Service Mesh
language: markdown
tags: [system-design, architecture, service-mesh, networking]
selection: null
isPinned: false
timestamp: 1781600000015
---
# Service Mesh

**Links**: [[Microservices Architecture]] | [[Docker Containers]] | [[Message Queues]] | [[Monitoring and Observability]] | [[Cloud Computing]]

## What is a Service Mesh?

A service mesh is a dedicated infrastructure layer that handles service-to-service communication in a microservices deployment. It offloads networking concerns from application code.

## Sidecar Pattern

Each service instance gets a proxy sidecar (Envoy) that handles all network traffic:

```
┌─────────────────┐
│  Service A       │
│  (application)   │
├─────────────────┤
│  Envoy Proxy     │ ← Sidecar
└────────┬────────┘
         │ mTLS, routing, metrics
         │
┌────────┴────────┐
│  Envoy Proxy     │
├─────────────────┤
│  Service B       │
│  (application)   │
└─────────────────┘
```

## Istio Architecture

| Component | Function |
|-----------|----------|
| **Envoy** | Sidecar proxy (data plane) |
| **Pilot** | Traffic management, service discovery |
| **Citadel** | Certificate authority, mTLS |
| **Galley** | Configuration validation |

## Capabilities

| Feature | Description | Without Mesh | With Mesh |
|---------|-------------|--------------|-----------|
| mTLS | Encrypted service-to-service | Custom code | Automatic |
| Retries | Automatic retry on failure | Library code | Configurable |
| Circuit breaking | Fail fast on downstream failure | Manual | Policy-based |
| Traffic splitting | Canary, blue-green deployments | Load balancer | Fine-grained |
| Observability | Metrics, traces, logs | Instrumentation | Automatic |
| Rate limiting | Throttle requests | Application code | Proxy-level |

## Traffic Routing Example (Istio)

```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - match:
    - headers:
        version:
          exact: v2
    route:
    - destination:
        host: reviews
        subset: v2
      weight: 100
  - route:
    - destination:
        host: reviews
        subset: v1
      weight: 100
```

**Next**: [[Chaos Engineering]] — Test system resilience