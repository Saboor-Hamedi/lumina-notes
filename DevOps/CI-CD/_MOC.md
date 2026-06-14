---
id: moc-cicd-0000-0000-0000-000000000014
title: CI/CD — Map of Content
language: markdown
tags: [moc, index, cicd]
isPinned: true
timestamp: 1781700000014
---

# 🔄 CI/CD — Map of Content

**Parent**: [[DevOps/_MOC|DevOps]]

## Topics

| Tool | Description | Key Features |
|------|-------------|--------------|
| [[DevOps/CI-CD/CI CD Pipelines]] | Core CI/CD concepts | Stages, gates, artifacts |
| [[DevOps/CI-CD/GitHub Actions]] | GitHub-native CI/CD | YAML workflows, matrix builds |
| [[DevOps/CI-CD/GitLab CI]] | GitLab-integrated CI/CD | Auto DevOps, review apps |
| [[DevOps/CI-CD/Jenkins]] | Self-hosted automation | Plugins, pipelines as code |

## Pipeline Stages

```
Code → Build → Test → Deploy → Monitor
  ↑                        │
  └────── Rollback ←───────┘
```

## Cross-Domain Links

- [[DevOps/CI-CD/CI CD Pipelines]] → [[Testing/API Testing]], [[Testing/Unit Testing Guide]], [[Git/Git Workflows]]
- [[DevOps/CI-CD/GitHub Actions]] → [[AI-ML/Deep-Learning/Machine-Learning/MLOps]], [[Testing/Load Testing]]
