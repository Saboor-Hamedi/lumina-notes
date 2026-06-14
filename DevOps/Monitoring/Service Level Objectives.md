---
id: a1b2c3d4-1147-4000-8000-000000000147
title: Service Level Objectives
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001147
---
# Service Level Objectives

SLOs define the target reliability level for a service. They're the foundation of data-driven reliability engineering.

## Definitions

| Term | Definition | Example |
|------|------------|---------|
| SLI (Service Level Indicator) | A quantifiable measure | Request latency p99 |
| SLO (Service Level Objective) | Target value for an SLI | p99 latency < 500ms, 99.9% of the time |
| SLA (Service Level Agreement) | Contractual commitment to customers | 99.95% uptime, 5% refund if missed |

## Common SLIs

| Category | SLI | Measurement |
|----------|-----|-------------|
| Availability | Request success rate | `successful / total` |
| Latency | Response time distribution | p50, p95, p99 |
| Throughput | Requests per second | Count/time window |
| Durability | Data not lost | `stored / acknowledged` |
| Freshness | Data age | `now - last_update` |
| Correctness | Error rate | `errors / total` |

## SLO Definition Example

```yaml
service: orders-api
slo:
  - name: request-latency
    indicator: latency_p99
    target: 500ms
    window: 28d
    compliance: 99.9%
  - name: availability
    indicator: success_rate
    target: 99.95%
    window: 28d
    compliance: 99.9%
  - name: throughput
    indicator: requests_per_second
    target: 10000
    window: 1h
    compliance: 99.9%
```

## Error Budget

```
Error Budget = 1 - SLO
Example: 99.9% SLO → 0.1% error budget = 43.2 minutes/month

Error budget policy:
- While budget remains: Deploy freely, add features
- Budget exhausted: Freeze deployments, focus on reliability
```

```python
def error_budget_remaining(slo_target, window_seconds):
    allowed_errors = (1 - slo_target) * total_requests
    actual_errors = count_errors(window_seconds)
    budget_remaining = (allowed_errors - actual_errors) / allowed_errors
    return max(0, min(1, budget_remaining))

if error_budget_remaining(0.999, 30 * 24 * 3600) < 0.1:
    ci_pipeline.fail("Error budget exhausted — reliability freeze")
```

## Multi-Window, Multi-Burn-Rate Alerts

Alert when error budget is burning too fast:

| Burn Rate | Window | Alert Condition |
|-----------|--------|----------------|
| 1x | 1h | Budget exhaust in 1h at current rate |
| 2x | 6h | Budget exhaust in 3h at 2x rate |
| 10x | 30m | Budget exhaust in 3m at 10x rate |

```yaml
# Prometheus alerting rules
groups:
  - name: slo
    rules:
      - alert: HighErrorBudgetBurn
        expr: |
          (
            (1 - (sum(rate(errors_total[1h])) / sum(rate(requests_total[1h]))))
            > 0.999 * 0.1
          ) or (
            (1 - (sum(rate(errors_total[6h])) / sum(rate(requests_total[6h]))))
            > 0.999 * 0.05
          )
        for: 5m
        labels:
          severity: page
```

## SLO Tiers

| Tier | SLO | Cost | Use Case |
|------|-----|------|----------|
| Critical | 99.99% | High | Payments, auth |
| Standard | 99.9% | Medium | Core business logic |
| Best Effort | 99% | Low | Background jobs, analytics |

## Implementing SLOs

```python
from datetime import datetime, timedelta
from collections import deque

class SLOMonitor:
    def __init__(self, slo_target=0.999, window_days=28):
        self.slo_target = slo_target
        self.window = timedelta(days=window_days)
        self.events = deque()

    def record(self, success: bool, duration_ms: float):
        self.events.append({
            "success": success,
            "duration_ms": duration_ms,
            "timestamp": datetime.now()
        })
        self._trim()

    def availability(self):
        self._trim()
        if not self.events: return 1.0
        successes = sum(1 for e in self.events if e["success"])
        return successes / len(self.events)

    def latency_p99(self):
        self._trim()
        if not self.events: return 0
        durations = sorted(e["duration_ms"] for e in self.events)
        idx = int(len(durations) * 0.99)
        return durations[idx]

    def meets_slo(self):
        return self.availability() >= self.slo_target

    def _trim(self):
        cutoff = datetime.now() - self.window
        while self.events and self.events[0]["timestamp"] < cutoff:
            self.events.popleft()
```

## Common Pitfalls

| Pitfall | Mitigation |
|---------|------------|
| Too many SLOs | Start with 3-5, focus on user-facing metrics |
| SLOs vs SLIs | Define SLIs first, then set targets |
| Unrealistic targets | Baseline first, then set SLOs |
| Alert fatigue | Use burn-rate alerts, not threshold |
| Ignoring SLOs | Integrate into CI/CD, team reviews |

**Links**: [[Observability and Monitoring]] | [[Prometheus and Monitoring]] | [[Incident Response]] | [[Site Reliability Engineering]] | [[Performance Testing]]
