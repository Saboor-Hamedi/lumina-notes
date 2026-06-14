---
id: a1b2c3d4-1203-4000-8000-000000000203
title: Grafana Deep Dive
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001203
---
# Grafana Deep Dive

Grafana is an open-source observability platform for visualizing metrics, logs, and traces from any data source.

## Architecture

```
Data Sources
  ├── Prometheus (metrics)
  ├── Loki (logs)
  ├── Tempo (traces)
  ├── Graphite
  ├── InfluxDB
  ├── Elasticsearch
  ├── CloudWatch
  ├── Azure Monitor
  ├── Google Cloud Monitoring
  └── 50+ more plugins
      │
Grafana Server
  ├── Query layer (data source abstraction)
  ├── Alerting engine
  ├── Dashboard rendering
  └── Authentication / Authorization
      │
  Clients (Web UI, API, Mobile)
```

## PromQL Queries

```promql
# Basic queries
http_requests_total                        # All time series
http_requests_total{status="200"}          # Filter by label
rate(http_requests_total[5m])              # Per-second rate over 5 min
sum by (method) (rate(http_requests_total[5m]))  # Aggregate by method

# Common patterns
# Error rate
sum(rate(http_requests_total{status=~"5.."}[5m]))
/
sum(rate(http_requests_total[5m]))
* 100

# Latency percentiles
histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))

# CPU usage
100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Memory
node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes
```

## Dashboard Panels

```json
{
    "title": "API Overview",
    "panels": [
        {
            "title": "Request Rate",
            "type": "timeseries",
            "datasource": "Prometheus",
            "targets": [{
                "expr": "sum(rate(http_requests_total[5m]))",
                "legendFormat": "Total"
            }],
            "fieldConfig": {
                "defaults": {
                    "unit": "cps",
                    "thresholds": {
                        "steps": [
                            { "value": null, "color": "green" },
                            { "value": 1000, "color": "yellow" },
                            { "value": 2000, "color": "red" }
                        ]
                    }
                }
            }
        },
        {
            "title": "Error Rate",
            "type": "gauge",
            "description": "Current error rate percentage",
            "targets": [{
                "expr": "sum(rate(http_requests_total{status=~\"5..\"}[5m])) / sum(rate(http_requests_total[5m])) * 100"
            }],
            "fieldConfig": {
                "defaults": {
                    "unit": "percent",
                    "min": 0,
                    "max": 5,
                    "thresholds": {
                        "steps": [
                            { "value": null, "color": "green" },
                            { "value": 1, "color": "yellow" },
                            { "value": 3, "color": "red" }
                        ]
                    }
                }
            }
        }
    ]
}
```

## LogQL (Loki Queries)

```logql
# Basic log queries
{app="nginx"} |= "error"
{app="api"} |= "5xx" | json | status_code > 500

# Log metrics
rate({app="nginx"} |= "error"[5m])

# Aggregations
topk(10, sum by (path) (rate({app="api"} |= "4xx"[5m])))

# Label extraction
{app="api"} | json | line_format "{{.method}} {{.path}} {{.status}}"

# Pattern matching
{app="api"} | pattern "<ip> - - <_> \"<method> <path> <_>\" <status> <_>"
```

## Alerting

```yaml
# Grafana alert rule
apiVersion: 1
groups:
  - name: api_alerts
    folder: API
    interval: 30s
    rules:
      - uid: api_high_error_rate
        title: "High API Error Rate"
        condition: "A"
        expressions:
          - refId: "A"
            type: "query"
            datasourceUid: "prometheus"
            expression: |
              sum(rate(http_requests_total{status=~"5.."}[5m]))
              /
              sum(rate(http_requests_total[5m]))
              * 100 > 5
          - refId: "B"
            type: "classic_conditions"
            expression: "A > 5"
        no_data_state: "NoData"
        for: "5m"
        labels:
          severity: critical
        annotations:
          summary: "Error rate above 5%"
          description: "Current error rate: {{ $values.A.Value }}%"
        notification_settings:
          # Contact point: slack, email, pagerduty
```

## Provisioning

```yaml
# datasources.yaml
apiVersion: 1
datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
    editable: false

  - name: Loki
    type: loki
    access: proxy
    url: http://loki:3100
    jsonData:
      maxLines: 5000

  - name: Tempo
    type: tempo
    access: proxy
    url: http://tempo:3200
    jsonData:
      tracesToLogs:
        datasourceUid: loki
        tags: ['instance', 'pod']
        mappedTags: [{ key: 'service.name', value: 'service' }]
```

```yaml
# dashboards.yaml
apiVersion: 1
providers:
  - name: "Default"
    orgId: 1
    folder: "Production"
    type: file
    disableDeletion: false
    updateIntervalSeconds: 10
    options:
      path: /etc/grafana/dashboards
```

## Variables and Templating

```yaml
# Dashboard variables
- name: environment
  type: custom
  query: production, staging, development
  current: production

- name: service
  type: query
  datasource: Prometheus
  query: label_values(up, service)
  multi: true
  includeAll: true

- name: instance
  type: query
  datasource: Prometheus
  query: label_values(up{service="$service"}, instance)
```

```promql
# Use variables in queries
sum(rate(http_requests_total{environment="$environment", service=~"$service"}[5m]))
```

## Authentication

```yaml
# auth.generic_oauth (GitHub, Google, GitLab)
[auth.github]
enabled = true
allow_sign_up = true
client_id = ${GITHUB_CLIENT_ID}
client_secret = ${GITHUB_CLIENT_SECRET}
scopes = user:email,read:org
allowed_organizations = [my-org]

# Role mapping
[auth]
oauth_auto_login = true
oauth_skip_org_role_update_sync = true
```

## API Usage

```bash
# Create API key
# Grafana UI → Configuration → API Keys

# Use API
curl -H "Authorization: Bearer glsa_xxx" https://grafana.example.com/api/search
curl -H "Authorization: Bearer glsa_xxx" https://grafana.example.com/api/dashboards/uid/abc123
```
