---
id: a1b2c3d4-1080-4000-8000-000000000080
title: Performance Testing
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001080
---

**Links**: [[Load Testing]] | [[Integration Testing Patterns]] | [[Software Testing Pyramid]] | [[Visual Regression Testing]] | [[Unit Testing Guide]] | [[API Testing]]


# Performance Testing

Performance testing measures system speed, responsiveness, stability, and scalability under various load conditions.

## Types of Performance Testing

| Type | Purpose | Question Answered |
|------|---------|-------------------|
| Load Testing | Expected traffic behavior | "Can it handle normal load?" |
| Stress Testing | Beyond capacity behavior | "What breaks and how?" |
| Spike Testing | Sudden traffic surges | "Does it survive a flash crowd?" |
| Soak Testing | Extended duration stability | "Does it degrade over time?" |
| Endurance Testing | Memory leaks over time | "Does it leak resources?" |
| Scalability Testing | Horizontal/vertical scaling | "Does adding resources help?" |

## Key Metrics

| Metric | Description | Good Target |
|--------|-------------|-------------|
| Response time (P50) | Average user wait | < 200ms |
| Response time (P95) | Typical worst case | < 500ms |
| Response time (P99) | Genuine worst case | < 1000ms |
| Throughput (RPS) | Requests per second | Depends on system |
| Error rate | % failed requests | < 0.1% |
| CPU usage | Server utilization | < 80% |
| Memory usage | Heap + RSS | < 80% of available |
| Connection pool | DB connections in use | < 80% of pool |

## Load Testing Tools

| Tool | Language | Protocol | Best For |
|------|----------|----------|----------|
| k6 | JavaScript (Go engine) | HTTP, gRPC, WebSocket | Developer-friendly |
| Locust | Python | HTTP | Programmable scenarios |
| JMeter | Java GUI | HTTP, JDBC, JMS | Enterprise, complex protocols |
| Gatling | Scala (DSL) | HTTP | High-scale, CI-friendly |
| Vegeta | Go | HTTP | Ad-hoc CLI testing |
| wrk | C (Lua scripting) | HTTP | Raw throughput benchmarks |

## k6 Example

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';
import { Rate } from 'k6/metrics';

const errorRate = new Rate('errors');

export const options = {
  stages: [
    { duration: '2m', target: 100 },  // ramp up
    { duration: '5m', target: 100 },  // stay
    { duration: '2m', target: 0 },    // ramp down
  ],
  thresholds: {
    http_req_duration: ['p(95)<500', 'p(99)<1500'],
    errors: ['rate<0.01'],
  },
};

export default function () {
  const res = http.get('https://api.example.com/users');
  check(res, {
    'status is 200': (r) => r.status === 200,
    'response time < 300ms': (r) => r.timings.duration < 300,
  });
  errorRate.add(res.status !== 200);
  sleep(1);
}
```

## Locust Example

```python
from locust import HttpUser, task, between

class WebsiteUser(HttpUser):
    wait_time = between(1, 3)

    @task(3)
    def view_users(self):
        self.client.get("/api/users")

    @task(1)
    def create_user(self):
        self.client.post("/api/users", json={
            "name": "Test User",
            "email": "test@example.com"
        })

    def on_start(self):
        self.client.post("/login", json={"username": "test", "password": "test"})
```

## Performance Testing Strategy

```
    Production-like Environment
              ↓
    Define Performance Requirements
    (response time, throughput, error rate)
              ↓
    Create Test Scenarios
    (realistic user behavior mix)
              ↓
    Run Tests (baseline → load → stress → soak)
              ↓
    Analyze Bottlenecks
    (slow queries, high CPU, lock contention)
              ↓
    Optimize & Retest
```

## Common Bottlenecks

| Layer | Bottleneck | Fix |
|-------|------------|-----|
| Web server | Thread pool exhausted | Increase threads, async IO |
| Application | Slow algorithm | Profiling, optimization |
| Database | Missing index | Add index, denormalize |
| Database | N+1 queries | Eager loading, batching |
| Cache | Low hit rate | Warm cache, increase TTL |
| Network | Bandwidth saturation | Compression, CDN |
| Locking | Contention | Reduce scope, use optimistic locks |

## Tuning vs Optimization

| Tuning | Optimization |
|--------|-------------|
| Change configuration | Change code/architecture |
| Pool sizes, timeouts, query limits | Better algorithm, caching layer |
| Quick, low risk | Slower, higher risk |
| Diminishing returns | Step function improvements |

## Reporting

```python
# Sample report structure
report = {
    "test_info": {
        "type": "load_test",
        "environment": "staging",
        "timestamp": "2026-06-13T10:00:00Z"
    },
    "results": {
        "total_requests": 50000,
        "p50_latency_ms": 145,
        "p95_latency_ms": 380,
        "p99_latency_ms": 890,
        "throughput_rps": 1250,
        "error_rate": 0.002,
        "peak_cpu": 75,
        "peak_memory_gb": 12.5
    },
    "bottlenecks": [
        {
            "severity": "high",
            "component": "database",
            "finding": "Slow query on orders table",
            "recommendation": "Add composite index on (user_id, created_at)"
        }
    ]
}
```

**Links**: [[Load Testing]] | [[API Testing]] | [[Performance Profiling]] | [[SQL Query Optimization]] | [[Caching Strategies]]
