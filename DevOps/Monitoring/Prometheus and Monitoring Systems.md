---
id: a1b2c3d4-1067-4000-8000-000000000067
title: Prometheus and Monitoring Systems
language: markdown
tags:
  - devops
  - monitoring
  - prometheus
selection: null
isPinned: false
timestamp: 1781769189391
---
# Prometheus and Monitoring Systems

Prometheus is an open-source monitoring and alerting toolkit designed for reliability and scalability. It collects metrics from targets via HTTP pull model.

## Prometheus Architecture

```
┌──────────┐     scrape      ┌──────────────┐
│ Service A │ ←────────────── │  Prometheus  │
│ /metrics  │                 │    Server    │
└──────────┘                 │              │
                              │ TSDB + Rules │
┌──────────┐     scrape      │              │
│ Service B │ ←────────────── │  Alertmanager│
│ /metrics  │                 └──────┬───────┘
└──────────┘                         │
                              ┌──────┴───────┐
                              │   Alerting   │
                              │ (Slack, Pager)│
                              └──────────────┘
                              ┌──────────────┐
                              │  Grafana     │
                              │ (Dashboards) │
                              └──────────────┘
```

## Pull Model vs Push Model

| Aspect | Pull (Prometheus) | Push (Graphite, Datadog) |
|--------|-------------------|--------------------------|
| Discovery | Service discovery | Agent config |
| Control | Prometheus controls scrape | Targets control send |
| Reliability | Degraded targets visible | Missing data = unknown |
| Scaling | Federation, sharding | Forwarders, aggregators |

## Metric Types

| Type | Description | Example |
|------|-------------|---------|
| Counter | Only increases | Request count, errors total |
| Gauge | Goes up and down | CPU usage, memory, queue size |
| Histogram | Sampled observations (buckets) | Request latency distribution |
| Summary | Sampled + quantiles | P99 latency |

## Instrumentation

```python
from prometheus_client import Counter, Histogram, generate_latest

REQUESTS = Counter('http_requests_total', 'Total HTTP requests',
                   ['method', 'endpoint', 'status'])
LATENCY = Histogram('http_request_duration_seconds', 'Request latency',
                    ['method'], buckets=[0.01, 0.05, 0.1, 0.5, 1, 5])

@app.route('/users')
def get_users():
    with LATENCY.labels(method='GET').time():
        users = fetch_users()
    REQUESTS.labels(method='GET', endpoint='/users', status=200).inc()
    return jsonify(users)
```

## PromQL (Prometheus Query Language)

```promql
# Rate of requests per second over last 5 minutes
rate(http_requests_total[5m])

# 95th percentile request latency
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))

# CPU usage by instance
100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Alert: error rate > 5%
rate(http_requests_total{status=~"5.."}[5m]) / rate(http_requests_total[5m]) > 0.05
```

## Alerting Rules

```yaml
groups:
  - name: example
    rules:
      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.05
        for: 5m
        annotations:
          summary: "High error rate on {{ $labels.instance }}"
          description: "Error rate is {{ $value | humanizePercentage }}"
```

## Service Discovery

| Platform | Integration |
|----------|-------------|
| Kubernetes | Pod/service annotations (default) |
| AWS EC2 | EC2 API tags |
| Consul | Consul catalog |
| File-based | YAML/JSON target lists |
| DNS | SRV record lookups |

## Exporters

| Exporter | What It Monitors |
|----------|-----------------|
| node_exporter | OS/hardware metrics |
| blackbox_exporter | HTTP/DNS/TCP probes |
| postgres_exporter | PostgreSQL metrics |
| redis_exporter | Redis metrics |
| kube_state_metrics | Kubernetes objects |

## Thanos (Prometheus at Scale)

Extends Prometheus with:
- **Global query view** across multiple Prometheus instances
- **Unlimited retention** (object storage)
- **Downsampling** for fast historical queries
- **High availability** with sidecar pattern

**Links**: [[Monitoring and Observability]] | [[Logging Best Practices]] | [[Kubernetes Basics]] | [[Incident Response]] | [[Docker Compose]]
