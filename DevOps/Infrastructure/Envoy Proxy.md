---
id: a1b2c3d4-1101-4000-8000-000000000101
title: Envoy Proxy
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001101
---

**Links**: [[Consul]] | [[API Gateway Patterns]] | [[Nginx Configuration]] | [[Infrastructure as Code]] | [[Terraform]] | [[Ansible]]


# Envoy Proxy

Envoy is a high-performance L3/L4/L7 proxy designed for service mesh architectures. It's the default data plane for Istio and can function as API gateway, edge proxy, or sidecar.

## Architecture

```
┌─────────────────────────────────┐
│         Envoy Proxy             │
│                                 │
│  ┌──────────┐  ┌──────────┐   │
│  │ Listener  │  │ Filter   │   │
│  │ :443      │──► Chain    │   │
│  └──────────┘  └────┬─────┘   │
│                     │         │
│  ┌──────────────────▼──────┐  │
│  │      Cluster            │  │
│  │  ┌───┐ ┌───┐ ┌───┐     │  │
│  │  │E1 │ │E2 │ │E3 │     │  │
│  │  └───┘ └───┘ └───┘     │  │
│  └─────────────────────────┘  │
│                                 │
│  xDS API ← Control Plane       │
└─────────────────────────────────┘
```

## Key Features

| Feature | Description |
|---------|-------------|
| L3/L4 proxy | TCP/UDP proxying |
| L7 proxy | HTTP/1.1, HTTP/2, gRPC |
| Hot reload | Reload config without restarting |
| xDS API | Dynamic configuration via control plane |
| Load balancing | Round robin, least request, ring hash, maglev |
| Circuit breaking | Automatic ejection of unhealthy hosts |
| Retries | Configurable retry policies |
| Rate limiting | Local + global rate limiting |
| Tracing | Zipkin, Jaeger, Datadog, OpenTelemetry |

## Configuration (Bootstrap)

```yaml
admin:
  address:
    socket_address: { address: 127.0.0.1, port_value: 9901 }

static_resources:
  listeners:
    - name: listener_0
      address:
        socket_address: { address: 0.0.0.0, port_value: 8080 }
      filter_chains:
        - filters:
            - name: envoy.filters.network.http_connection_manager
              typed_config:
                "@type": type.googleapis.com/envoy.extensions.filters.network.http_connection_manager.v3.HttpConnectionManager
                stat_prefix: ingress_http
                route_config:
                  name: local_route
                  virtual_hosts:
                    - name: backend
                      domains: ["*"]
                      routes:
                        - match: { prefix: "/api/" }
                          route: { cluster: service_api }
                http_filters:
                  - name: envoy.filters.http.router

  clusters:
    - name: service_api
      connect_timeout: 0.25s
      type: STRICT_DNS
      lb_policy: ROUND_ROBIN
      load_assignment:
        cluster_name: service_api
        endpoints:
          - lb_endpoints:
              - endpoint:
                  address:
                    socket_address: { address: api.example.com, port_value: 8080 }
```

## xDS API (Discovery Service)

| API | Resource | Purpose |
|-----|----------|---------|
| LDS | Listeners | What ports to listen on |
| RDS | Routes | How to route requests |
| CDS | Clusters | Where to send traffic |
| EDS | Endpoints | Individual backend instances |
| SDS | Secrets | TLS certificates |
| SRDS | Scoped Routes | Route scoping |

## Load Balancing

```yaml
clusters:
  - name: service
    type: STRICT_DNS
    lb_policy: LEAST_REQUEST  # ROUND_ROBIN | RING_HASH | RANDOM | MAGLEV
    circuit_breakers:
      thresholds:
        - priority: DEFAULT
          max_connections: 1000
          max_pending_requests: 1000
          max_requests: 2000
          max_retries: 3
    outlier_detection:
      consecutive_5xx: 5
      interval: 30s
      base_ejection_time: 30s
      max_ejection_percent: 50
```

## Access Logging

```yaml
access_log:
  - name: envoy.access_loggers.file
    typed_config:
      "@type": type.googleapis.com/envoy.extensions.access_loggers.file.v3.FileAccessLog
      path: /var/log/envoy/access.log
      format: "%REQ(:METHOD)% %REQ(X-ENVOY-ORIGINAL-PATH?:PATH)% %RESPONSE_CODE% %DURATION%"
```

## vs NGINX

| Aspect | Envoy | NGINX |
|--------|-------|-------|
| Config | Dynamic (xDS API) | Static (reload) |
| Protocol | HTTP/2, gRPC native | HTTP/2 via module |
| Service mesh | Designed for it | Not designed for it |
| Observability | Built-in metrics, tracing | Module-dependent |
| Configuration model | Structured YAML | Declarative directives |
| Thread model | Single process, multi-threaded | Multiple worker processes |

**Links**: [[Istio Service Mesh]] | [[Nginx Configuration]] | [[Service Mesh]] | [[Microservices Architecture]] | [[API Gateway]]
