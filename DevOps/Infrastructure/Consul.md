---
id: a1b2c3d4-1102-4000-8000-000000000102
title: Consul
language: markdown
tags: [devops, infrastructure, consul, service-discovery]
selection: null
isPinned: false
timestamp: 1781500001102
---

**Links**: [[Envoy Proxy]] | [[Terraform]] | [[Infrastructure as Code]] | [[Ansible]] | [[Cloud Computing]] | [[Helm]]


# Consul

Consul is a service mesh solution providing service discovery, health checking, configuration, and secure communication across datacenters.

## Core Features

| Feature | Description |
|---------|-------------|
| Service Discovery | DNS and HTTP API for finding services |
| Health Checking | Distributed health monitoring |
| KV Store | Configuration and feature flags |
| Multi-Datacenter | Native cross-datacenter replication |
| Service Mesh | L7 traffic management + mTLS |
| Service Segmentation | Intent-based access control |

## Architecture

```
Consul Server Cluster (Raft consensus)
    │
    ├── Consul Client (node A)
    │     ├── Service A (sidecar proxy)
    │     └── Service B (sidecar proxy)
    │
    ├── Consul Client (node B)
    │     └── Service C
    │
    └── Consul Client (node C)
          └── Service D
```

## Service Registration

```json
{
  "service": {
    "name": "api",
    "port": 8080,
    "tags": ["v1", "production"],
    "checks": [
      {
        "name": "HTTP Health",
        "http": "http://localhost:8080/health",
        "interval": "10s",
        "timeout": "2s",
        "deregister_critical_service_after": "30s"
      }
    ],
    "meta": {
      "version": "1.0.0",
      "owner": "platform-team"
    }
  }
}
```

## Service Discovery

```bash
# DNS interface
dig api.service.consul

# HTTP API
curl http://localhost:8500/v1/catalog/service/api
curl http://localhost:8500/v1/health/service/api?passing=true

# Client library (Go)
client, _ := consul.NewClient(consul.DefaultConfig())
services, _ := client.Agent().Services()
```

## KV Store

```bash
# CLI
consul kv put config/myapp/feature_flags/users_v2 true
consul kv get config/myapp/feature_flags/users_v2

# Lock-free, watches for changes
consul watch -type=key -key=config/myapp/feature_flags/users_v2
```

## Connect (Service Mesh)

```hcl
service {
  name = "web"
  port = 8080
  
  connect {
    sidecar_service {
      proxy {
        upstreams {
          destination_name = "api"
          local_bind_port  = 8081
        }
      }
    }
  }
}
```

```hcl
# Intentions (access control)
kind = "service-intentions"
name = "api"
sources {
  name   = "web"
  action = "allow"
}
```

## Service Split (Traffic Management)

```hcl
kind = "service-splitter"
name = "api"
splits = [
  { weight = 90, service_subset = "v1" },
  { weight = 10, service_subset = "v2" },
]
```

## Consul Template

```hcl
# Dynamic configuration from Consul
template {
  source      = "/etc/nginx/nginx.conf.ctmpl"
  destination = "/etc/nginx/nginx.conf"
  command     = "systemctl reload nginx"
}
```

```nginx
{{- range service "api" }}
server {{ .Address }}:{{ .Port }} weight=1;
{{- end }}
```

## Server Consensus (Raft)

| Protocol | Nodes | Failure Tolerance |
|----------|-------|------------------|
| Raft | 3 | 1 node |
| Raft | 5 | 2 nodes |
| Raft | 7 | 3 nodes |

**Links**: [[Service Mesh]] | [[Envoy Proxy]] | [[Service Discovery]] | [[Microservices Architecture]] | [[DNS Deep Dive]]
