---
id: a1b2c3d4-1146-4000-8000-000000000146
title: Distributed Tracing
language: markdown
tags: [devops, monitoring, distributed-tracing, observability]
selection: null
isPinned: false
timestamp: 1781500001146
---
# Distributed Tracing

Distributed tracing tracks requests as they flow through microservices, revealing latency bottlenecks, error sources, and service dependencies.

## Core Concepts

```
User ──► Gateway ──► Auth ──► Orders ──► Payments
                          │         │
                          │         └──► Inventory
                          │
                          └──► User Profile
```

Each request gets a **trace ID** propagated across all services, with individual units of work called **spans**.

## Trace Model

```yaml
Trace (root span):
  ID: trace_abc123
  └── Span: "HTTP GET /api/orders"
        ID: span_001
        Duration: 245ms
        Tags: { http.method: GET, http.status_code: 200 }
        └── Span: "OrdersService.getOrders"
              ID: span_002
              Duration: 180ms
              Tags: { db.statement: "SELECT * FROM orders" }
              └── Span: "Redis.getUserCache"
                    ID: span_003
                    Duration: 12ms
                    Tags: { cache.hit: true }
```

## Propagation

```javascript
// HTTP headers (W3C Trace Context)
// traceparent: 00-0af7651916cd43dd8448eb211c80319c-b7ad6b7169203331-01
//              |  |         trace-id          |    span-id     |flags|
//              version                                     trace-flags

// OpenTelemetry propagation
const { context, trace, propagation } = require("@opentelemetry/api");

async function makeRequest(url, headers = {}) {
    const activeContext = context.active();
    const carrier = {};
    propagation.inject(activeContext, carrier);

    const response = await fetch(url, {
        headers: { ...carrier, ...headers }
    });
    return response;
}
```

## Instrumentation with OpenTelemetry

```javascript
// Automatic instrumentation
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");
const { getNodeAutoInstrumentations } = require("@opentelemetry/auto-instrumentations-node");
const { OTLPTraceExporter } = require("@opentelemetry/exporter-trace-otlp-http");

const provider = new NodeTracerProvider({
    instrumentations: [getNodeAutoInstrumentations()]
});

provider.register();

// Manual spans
const tracer = trace.getTracer("my-service");

async function handleRequest(req, res) {
    const span = tracer.startSpan("handleRequest", {
        attributes: { "http.method": req.method, "http.path": req.path }
    });

    return await tracer.withActiveSpan(span, async () => {
        // ... do work ...
        const result = await processOrder(req.body);
        span.setAttribute("order.id", result.id);
        span.end();
        return result;
    });
}
```

## Span Attributes

```javascript
span.setAttribute("db.system", "postgresql");
span.setAttribute("db.statement", "SELECT * FROM orders WHERE id = $1");
span.setAttribute("db.user", "app_user");

// Events within a span
span.addEvent("cache.miss", { "cache.key": "user_123" });
span.addEvent("retry", { "attempt": 2, "delay_ms": 100 });

// Status
span.setStatus({ code: SpanStatusCode.ERROR, message: "Connection timeout" });
span.recordException(error);
```

## Sampling

| Strategy | Description | Use Case |
|----------|-------------|----------|
| Head-based | Sample decision at request start | Simple, default |
| Tail-based | Sample after request completes | Focus on slow/error traces |
| Probabilistic | Random % of all traces | Low traffic (10-100%) |
| Rate-limited | N traces per second | High traffic |
| Health-based | Sample + adapt based on system health | Adaptive |

```javascript
// Probabilistic sampler (10%)
const provider = new NodeTracerProvider({
    sampler: new TraceIdRatioBased(0.1)
});
```

## Backends

| Tool | Storage | Query Language | Self-Hosted |
|------|---------|---------------|-------------|
| Jaeger | Elasticsearch, Cassandra | Jaeger Query | Yes |
| Zipkin | Cassandra, Elasticsearch | Zipkin API | Yes |
| Grafana Tempo | Object store (S3, GCS) | TraceQL | Yes |
| Honeycomb | Proprietary | Honeycomb Query | No |
| Datadog | Proprietary | Datadog Query | No |
| Lightstep | Proprietary | Lightstep Query | No |

## TraceQL (Grafana Tempo)

```sql
{ .http.status_code = 500 && .service.name = "orders-service" } | rate() by(.db.system) | avg(duration) > 1s
```

## Common Use Cases

### Latency Breakdown

```
Find the span contributing most to total latency:
  Total: 2.5s
    ├── Auth: 1.2s  ← bottleneck
    ├── Orders: 0.8s
    └── Payments: 0.5s
```

### Error Detection

```
Error in root span:
  Find the deepest error span for root cause analysis →
  payments-service: "Connection refused to payment-gateway:443"
```

### Dependency Graph

```
Auto-generate service dependency graph:
  gateway → auth, orders, users
  orders → payments, inventory
```

## Best Practices

| Practice | Reason |
|----------|--------|
| Propagate trace context everywhere | Consistent trace across all services |
| Set meaningful span names | `"POST /api/orders"` not `"handler"` |
| Add business-relevant attributes | `order.id`, `customer.tier`, `payment.provider` |
| Set error status correctly | Distinguish expected errors from bugs |
| Keep spans focused | One logical operation per span |
| Avoid high-cardinality attributes | Don't add `user.id` if users = millions |
| Sample strategically | Don't trace 100% at high volume |

**Links**: [[Observability and Monitoring]] | [[Prometheus and Monitoring]] | [[Microservices]] | [[Incident Response]] | [[Logging Best Practices]]
