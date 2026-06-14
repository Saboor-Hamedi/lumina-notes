---
id: new-005-0000-0000-0000-000000000005
title: Monitoring and Observability
language: markdown
tags: [devops, monitoring, observability]
selection: null
isPinned: false
timestamp: 1781600000005
---
# Monitoring and Observability

**Links**: [[CI CD Pipelines]] | [[Cloud Computing]] | [[Docker Containers]] | [[Microservices Architecture]] | [[Log Management]] | [[Prometheus and Monitoring Systems]] | [[Grafana Deep Dive]] | [[OpenTelemetry Deep Dive]] | [[Distributed Tracing]] | [[Site Reliability Engineering]]

**See also**: [[Model Monitoring in Production]], [[Service Level Objectives]], [[Chaos Engineering]], [[Logging Best Practices]]

## The Three Pillars

| Pillar | What | Example Tools |
|--------|------|---------------|
| **Metrics** | Numerical measurements over time | Prometheus, Grafana |
| **Logs** | Time-stamped event records | ELK Stack, Loki, Datadog |
| **Traces** | Request propagation across services | Jaeger, Zipkin, OpenTelemetry |

## Prometheus Metrics

```yaml
# prometheus.yml
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: 'api'
    static_configs:
      - targets: ['localhost:8000']
```

Instrument your application:

```python
from prometheus_client import Counter, Histogram, generate_latest

REQUESTS = Counter('http_requests_total', 'Total HTTP requests')
LATENCY = Histogram('http_request_duration_seconds', 'Request latency')

@REQUESTS.count_exceptions()
def handle_request():
    with LATENCY.time():
        process()
```

## Grafana Dashboard

Queries and visualizes metrics from Prometheus and other sources.

```
Panel: Request Rate
Query: rate(http_requests_total[5m])

Panel: 95th Percentile Latency
Query: histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
```

## OpenTelemetry Tracing

```python
from opentelemetry import trace
tracer = trace.get_tracer(__name__)

with tracer.start_as_current_span("process_order") as span:
    span.set_attribute("order_id", order.id)
    result = payment_service.charge(order.total)
```

## Alerting

```yaml
# Alertmanager rules
groups:
  - name: api
    rules:
      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.05
        for: 5m
        labels:
          severity: critical
```

**Next**: [[API Documentation]] — Document your APIs