---
id: moc-mon-0000-0000-0000-000000000016
title: Monitoring & Observability — Map of Content
language: markdown
tags: [moc, index, monitoring]
isPinned: true
timestamp: 1781700000016
---

# 📊 Monitoring & Observability — Map of Content

**Parent**: [[DevOps/_MOC|DevOps]]

```mermaid
graph TD
  MON["Monitoring"] --> PILLARS["Three Pillars"]
  MON --> SRE["SRE Practices"]
  MON --> TOOLS["Tools"]

  PILLARS --> MET["Metrics"]
  PILLARS --> LOG["Logs"]
  PILLARS --> TRACE["Traces"]

  MET --> PROM["Prometheus"]
  MET --> GRAF["Grafana"]

  LOG --> LOG2["Logging Best Practices"]

  TRACE --> OTEL["OpenTelemetry"]
  TRACE --> DT["Distributed Tracing"]

  SRE --> SLO["Service Level Objectives"]
  SRE --> SRE2["Site Reliability Engineering"]
  SRE --> CE["Chaos Engineering"]
  SRE --> DRP["Disaster Recovery Planning"]

  TOOLS --> MO["Monitoring & Observability"]
  TOOLS --> CMD["Command Line Productivity"]
```

## Topics

| Category | Notes |
|----------|-------|
| **Metrics** | [[Prometheus and Monitoring Systems]], [[Grafana Deep Dive]] |
| **Logs** | [[Logging Best Practices]] |
| **Traces** | [[OpenTelemetry Deep Dive]], [[Distributed Tracing]] |
| **SRE** | [[Service Level Objectives]], [[Site Reliability Engineering]], [[Chaos Engineering]], [[Disaster Recovery Planning]] |
| **Overview** | [[Monitoring and Observability]], [[Command Line Productivity]] |

## Cross-Domain Links

- [[DevOps/Monitoring/Monitoring and Observability]] → [[AI-ML/Deep-Learning/Machine-Learning/Model Monitoring in Production]], [[System-Design/Architecture/Microservices Architecture]]
- [[DevOps/Monitoring/Prometheus and Monitoring Systems]] → [[AI-ML/Deep-Learning/Machine-Learning/Feature Stores for Machine Learning]]
- [[DevOps/Monitoring/OpenTelemetry Deep Dive]] → [[System-Design/Architecture/Distributed Tracing]], [[System-Design/Architecture/Microservices Architecture]]
- [[DevOps/Monitoring/Service Level Objectives]] → [[Testing/Performance Testing]], [[Testing/Load Testing]]
