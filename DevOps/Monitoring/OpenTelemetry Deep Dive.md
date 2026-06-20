---
id: a1b2c3d4-1179-4000-8000-000000000179
title: OpenTelemetry Deep Dive
language: markdown
tags:
  - devops
  - monitoring
  - opentelemetry
  - observability
selection: null
isPinned: false
customIcon: LucideTelescope
timestamp: 1781872480786
---
# OpenTelemetry Deep Dive

OpenTelemetry (OTel) is a vendor-neutral observability framework for collecting traces, metrics, and logs from applications.

## Architecture

```
Application
    │
    ├── OpenTelemetry SDK (instrumentation)
    │       │
    │       ├── TracerProvider (traces)
    │       ├── MeterProvider (metrics)
    │       └── LoggerProvider (logs)
    │
    ├── OpenTelemetry Collector
    │       │
    │       ├── Receivers (OTLP, Jaeger, Prometheus, Kafka)
    │       ├── Processors (batch, filter, sample, transform)
    │       └── Exporters (OTLP, Prometheus, Jaeger, S3, Datadog)
    │
    └── Backends (Jaeger, Tempo, Prometheus, Loki, Datadog, Honeycomb)
```

## Signals

| Signal | Description | Temporal | Example |
| --- | --- | --- | --- |
| Traces | Request flow across services | Spans in time | "HTTP GET /api/orders" |
| Metrics | Aggregated measurements | Points in time | request_count, latency_p99 |
| Logs | Discrete events with timestamp | Events | "Failed to connect to DB" |
| Baggage | Context propagation (key-value) | Throughout trace | "user_tier=premium" |


## Manual Instrumentation

```javascript
const { trace, context, SpanStatusCode } = require("@opentelemetry/api");
const tracer = trace.getTracer("payment-service");

async function processPayment(orderId, amount) {
    // Create a span
    const span = tracer.startSpan("processPayment", {
        attributes: {
            "payment.order_id": orderId,
            "payment.amount": amount,
        },
    });

    // Run work in span context
    return await context.with(trace.setSpan(context.active(), span), async () => {
        try {
            const result = await chargePayment(orderId, amount);
            span.setStatus({ code: SpanStatusCode.OK });
            span.setAttribute("payment.status", result.status);
            return result;
        } catch (error) {
            span.setStatus({ code: SpanStatusCode.ERROR, message: error.message });
            span.recordException(error);
            throw error;
        } finally {
            span.end();
        }
    });
}
```

## Automatic Instrumentation

```javascript
// Zero-code instrumentation via environment variables
// OTEL_METRICS_EXPORTER=otlp OTEL_TRACES_EXPORTER=otlp

// Or programmatically:
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");
const { getNodeAutoInstrumentations } = require("@opentelemetry/auto-instrumentations-node");
const { OTLPTraceExporter } = require("@opentelemetry/exporter-trace-otlp-http");
const { BatchSpanProcessor } = require("@opentelemetry/sdk-trace-base");

const provider = new NodeTracerProvider({
    instrumentations: [
        getNodeAutoInstrumentations({
            "@opentelemetry/instrumentation-http": {},
            "@opentelemetry/instrumentation-express": {},
            "@opentelemetry/instrumentation-redis": {},
            "@opentelemetry/instrumentation-pg": {},
            "@opentelemetry/instrumentation-grpc": {},
        }),
    ],
});

provider.addSpanProcessor(
    new BatchSpanProcessor(new OTLPTraceExporter({
        url: "http://otel-collector:4318/v1/traces",
    }))
);

provider.register();
```

## Context Propagation

```javascript
// W3C Trace Context: traceparent header
// Format: 00-{trace_id}-{span_id}-{flags}

// trace_id: 16 bytes (hex), unique per trace
// span_id:  8 bytes (hex), current span
// flags:    1 byte, 01 = sampled

// Manual propagation
const { propagation } = require("@opentelemetry/api");

// Inject context into outgoing request
function makeRequest(url) {
    const headers = {};
    propagation.inject(context.active(), headers);
    return fetch(url, { headers });
}

// Extract context from incoming request
app.use((req, res, next) => {
    const extractedContext = propagation.extract(context.active(), req.headers);
    context.with(extractedContext, next);
});
```

## OpenTelemetry Collector

```yaml
# otel-collector-config.yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318

processors:
  batch:
    timeout: 1s
    send_batch_size: 1024
  memory_limiter:
    check_interval: 1s
    limit_mib: 512
  attributes:
    actions:
      - key: environment
        value: production
        action: upsert
  filter:
    error_mode: ignore
    traces:
      span:
        - 'attributes["http.target"] == "/health"'

exporters:
  otlp:
    endpoint: "jaeger:4317"
    tls:
      insecure: true
  prometheus:
    endpoint: "0.0.0.0:8889"
  debug:
    verbosity: normal

service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [memory_limiter, batch, attributes, filter]
      exporters: [otlp, debug]
    metrics:
      receivers: [otlp]
      processors: [memory_limiter, batch]
      exporters: [prometheus, debug]
```

## Semantic Conventions

```javascript
// Standardized attribute names
span.setAttribute("http.method", "POST");
span.setAttribute("http.url", "https://api.example.com/orders");
span.setAttribute("http.status_code", 200);
span.setAttribute("http.request_content_length", 1024);

span.setAttribute("db.system", "postgresql");
span.setAttribute("db.name", "orders_db");
span.setAttribute("db.statement", "SELECT * FROM orders WHERE id = $1");
span.setAttribute("db.operation", "SELECT");

span.setAttribute("messaging.system", "kafka");
span.setAttribute("messaging.destination", "orders");
span.setAttribute("messaging.operation", "process");
```

## Sampling

```javascript
// Head-based sampling (decision at span start)
const { ParentBasedSampler, TraceIdRatioBased } = require("@opentelemetry/sdk-trace-node");

const provider = new NodeTracerProvider({
    sampler: new ParentBasedSampler({
        root: new TraceIdRatioBased(0.1),  // Sample 10% of root spans
    }),
});

// Tail-based sampling (decision after span complete)
// Requires collector-level sampling (e.g., Jaeger sampling strategy)
// Keeps complete traces, not individual spans
```

## Resources

```javascript
// Resource attributes identify the entity producing telemetry
const { Resource } = require("@opentelemetry/resources");
const { SemanticResourceAttributes } = require("@opentelemetry/semantic-conventions");

const provider = new NodeTracerProvider({
    resource: new Resource({
        [SemanticResourceAttributes.SERVICE_NAME]: "payment-service",
        [SemanticResourceAttributes.SERVICE_VERSION]: "1.2.3",
        [SemanticResourceAttributes.DEPLOYMENT_ENVIRONMENT]: "production",
        [SemanticResourceAttributes.HOST_NAME]: "payment-abc123",
        [SemanticResourceAttributes.CLOUD_REGION]: "us-east-1",
    }),
});
```

**Links**: [[Chaos Engineering]] | [[Command Line Productivity]] | [[Disaster Recovery Planning]] | [[Distributed Tracing]] | [[Grafana Deep Dive]] | [[Logging Best Practices]] | [[Monitoring and Observability]] | [[Prometheus and Monitoring Systems]] | [[Service Level Objectives]] | [[Site Reliability Engineering]]
