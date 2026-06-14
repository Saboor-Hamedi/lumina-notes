---
id: dev-067-0000-0000-0000-000000000056
title: Load Testing
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781900000008
---
# Load Testing

**Links**: [[API Testing]] | [[CI CD Pipelines]] | [[Kubernetes Deployments]] | [[Monitoring and Observability]] | [[Database Connection Pooling]]

## What is Load Testing?

Load testing simulates real-world traffic to measure system behavior under expected and peak loads.

## Types

| Type | Purpose | Example |
|------|---------|---------|
| **Smoke** | Minimal load, verify basic functionality | 1-2 concurrent users |
| **Load** | Expected traffic levels | 1000 users |
| **Stress** | Beyond expected limits | 10000+ users |
| **Spike** | Sudden traffic surges | 0 → 5000 users instantly |
| **Soak** | Extended period (hours) | Memory leaks over time |

## Locust (Python)

```python
from locust import HttpUser, task, between

class WebsiteUser(HttpUser):
    wait_time = between(1, 5)

    @task(3)
    def view_homepage(self):
        self.client.get("/")

    @task(1)
    def create_user(self):
        self.client.post("/users", json={
            "name": "Test User",
            "email": "test@example.com"
        })

    def on_start(self):
        self.client.post("/login", json={
            "username": "test",
            "password": "secret"
        })
```

```bash
locust -f locustfile.py --host=https://api.example.com
```

## k6 (JavaScript)

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
    stages: [
        { duration: '2m', target: 100 },  // Ramp up
        { duration: '5m', target: 100 },  // Stay
        { duration: '2m', target: 0 },    // Ramp down
    ],
    thresholds: {
        http_req_duration: ['p(95)<500'],  // 95% under 500ms
        http_req_failed: ['rate<0.01'],    // <1% errors
    },
};

export default function () {
    const res = http.get('https://api.example.com/users');
    check(res, { 'status 200': (r) => r.status === 200 });
    sleep(1);
}
```

## Key Metrics

| Metric | What It Measures |
|--------|-----------------|
| **Response time** | Latency (avg, p50, p95, p99) |
| **Throughput** | Requests per second |
| **Error rate** | Percentage of failed requests |
| **Concurrent users** | Active connections |
| **Resource usage** | CPU, memory, disk, network |

**Next**: [[API Testing]] — Validate your APIs