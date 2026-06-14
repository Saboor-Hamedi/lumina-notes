---
id: a1b2c3d4-1011-4000-8000-000000000011
title: Logging Best Practices
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001011
---
# Logging Best Practices

Logging provides observability into application behavior. Well-structured logs are essential for debugging, monitoring, and auditing.

## Log Levels

| Level | Severity | When to Use |
|-------|----------|-------------|
| TRACE | Finest detail | Step-by-step execution flow |
| DEBUG | Diagnostic | Development troubleshooting |
| INFO | Normal operation | Startup, shutdown, significant events |
| WARN | Unexpected but handled | Deprecated API, degraded mode |
| ERROR | Failure | Caught exceptions, operation failed |
| FATAL | Unrecoverable | Application cannot continue |

## Structured Logging

Prefer structured logs (JSON) over plain text for machine parsing:

```json
{"level":"ERROR","timestamp":"2026-06-13T10:00:00Z","logger":"payment","message":"Payment failed","transactionId":"txn_123","amount":49.99,"error":"insufficient_funds"}
```

## What to Log

| Do Log | Don't Log |
|--------|-----------|
| Request IDs for tracing | Passwords, secrets |
| Operation duration | Full credit card numbers |
| Business events | PII (without consent) |
| Error details with context | Stack traces for expected errors |
| State transitions | Excessive debug in production |

## Log Aggregation Tools

| Tool | Type | Features |
|------|------|----------|
| ELK Stack | Self-hosted | Elasticsearch, Logstash, Kibana |
| Loki | Lightweight | Prometheus-friendly, Grafana |
| Datadog | SaaS | Traces + metrics + logs |
| Splunk | Enterprise | Powerful search, expensive |
| Fluentd | Collector | Unified logging layer |

## Correlation IDs

Propagate a unique trace ID across services:

```
HTTP Request → API Gateway (generates trace_id)
    → Service A (logs with trace_id)
    → Service B (forwards trace_id)
    → Database (logs trace_id in slow query log)
```

## Log Rotation

Prevent disk exhaustion:

| Strategy | Description |
|----------|-------------|
| Size-based | Rotate after N MB (e.g., 100 MB) |
| Time-based | Rotate daily or hourly |
| Retention | Keep last N files or N days |
| Compression | Gzip rotated files |

## Anti-Patterns

- Logging in hot paths (tight loops degrades performance)
- Logging sensitive data
- Not including context (orphan messages)
- Over-logging at INFO level in production
- No log levels configured per environment

**Links**: [[Monitoring and Observability]] | [[Error Handling Patterns]] | [[Debugging Strategies]] | [[CI CD Pipelines]] | [[Microservices Architecture]]
