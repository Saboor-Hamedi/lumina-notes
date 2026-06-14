---
id: moc-infra-0000-0000-0000-000000000017
title: Infrastructure — Map of Content
language: markdown
tags: [moc, index, infrastructure]
isPinned: true
timestamp: 1781700000017
---

# 🏗️ Infrastructure — Map of Content

**Parent**: [[DevOps/_MOC|DevOps]]

## Topics

| Category | Notes |
|----------|-------|
| **IaC** | [[Infrastructure as Code]], [[Terraform]], [[Ansible]], [[Packer]], [[Vagrant]] |
| **Cloud** | [[Cloud Computing]], [[Cloud Cost Optimization]], [[Edge Computing and Edge AI]] |
| **Service Mesh & Proxy** | [[Envoy Proxy]], [[Consul]], [[Nginx Configuration]] |
| **Kubernetes Tools** | [[Helm]] |
| **Dev Environment** | [[Dev Environment Setup]], [[Developer Workflow Automation]], [[Shell Scripting]] |
| **Builds** | [[Build Tools]], [[Package Managers]] |

## The Infrastructure Stack

```
┌─────────────────────────────────────┐
│         Application Code             │
├─────────────────────────────────────┤
│  Service Mesh (Envoy, Consul)       │
├─────────────────────────────────────┤
│  Container Orchestration (K8s)      │
├─────────────────────────────────────┤
│  Container Runtime (Docker)         │
├─────────────────────────────────────┤
│  Provisioning (Terraform, Packer)   │
├─────────────────────────────────────┤
│  Configuration (Ansible)            │
├─────────────────────────────────────┤
│         Cloud / Bare Metal          │
└─────────────────────────────────────┘
```

## Cross-Domain Links

- [[DevOps/Infrastructure/Terraform]] → [[DevOps/Containers/Kubernetes Deployments]], [[DevOps/Infrastructure/Cloud Computing]]
- [[DevOps/Infrastructure/Cloud Computing]] → [[System-Design/Architecture/Serverless Computing]], [[System-Design/Databases/Database Sharding]]
- [[DevOps/Infrastructure/Helm]] → [[DevOps/Containers/Kubernetes Deployments]], [[Security/Kubernetes Security]]
