---
id: new-016-0000-0000-0000-000000000016
title: Chaos Engineering
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781600000016
---
# Chaos Engineering

**Links**: [[Service Mesh]] | [[Monitoring and Observability]] | [[Microservices Architecture]] | [[CI CD Pipelines]] | [[Error Handling Patterns]]

## What is Chaos Engineering?

Chaos engineering is the practice of intentionally injecting failures into a system to uncover weaknesses before they cause real outages.

## Principles

1. **Define steady state**: What does "normal" look like? (e.g., 99.9% request success, p99 latency < 200ms)
2. **Hypothesize**: What do we expect to happen when we inject a failure?
3. **Run experiment**: Introduce the failure in a controlled manner
4. **Analyze**: Compare results against steady state
5. **Fix and expand**: Address weaknesses, increase experiment scope

## Types of Experiments

| Experiment | Description |
|------------|-------------|
| **Kill a pod** | Randomly terminate a container |
| **Network latency** | Add delay to network requests |
| **Packet loss** | Drop network packets |
| **CPU stress** | Saturate CPU resources |
| **Memory pressure** | Fill up memory |
| **Disk I/O spike** | Increase disk latency |
| **DNS failure** | Make DNS resolution fail |
| **Certificate expiry** | Use expired TLS certificates |

## Tools

| Tool | Platform | Features |
|------|----------|----------|
| **Chaos Monkey** | Netflix/OSS | Random instance termination |
| **Gremlin** | SaaS | Safe failure injection, UI |
| **Litmus** | Kubernetes | Cloud-native chaos |
| **Chaos Mesh** | Kubernetes | Network, DNS, disk, stress |

## Kubernetes Chaos (Chaos Mesh)

```yaml
apiVersion: chaos-mesh.org/v1alpha1
kind: PodChaos
metadata:
  name: pod-kill-example
spec:
  action: pod-kill
  mode: one
  selector:
    namespaces:
      - production
    labelSelectors:
      app: payment-service
  duration: "60s"
```

## Safety

- **Blast radius**: Always start small (one instance, not all)
- **Run in production**: Only in systems designed for it (redundancy, graceful degradation)
- **Automated rollback**: Kill switch if metrics degrade beyond threshold
- **Business hours**: Start during low-traffic periods

**Next**: [[Time Series Databases]] — Store and query time-based data