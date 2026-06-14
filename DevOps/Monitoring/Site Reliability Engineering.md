---
id: a1b2c3d4-1073-4000-8000-000000000073
title: Site Reliability Engineering
language: markdown
tags: [devops, monitoring, sre, reliability]
selection: null
isPinned: false
timestamp: 1781500001073
---
# Site Reliability Engineering

Site Reliability Engineering (SRE) applies software engineering to operations — creating scalable, reliable, and efficient production systems.

## SRE Principles (Google)

| Principle | Meaning |
|-----------|---------|
| Embrace risk | 100% reliability is wrong goal; target is "good enough" |
| Service Level Objectives (SLOs) | Define measurable reliability targets |
| Eliminate toil | Automate repetitive operational work |
| Monitor with SLIs/SLOs/SLAs | Everything is measured |
| Reduce silos | Dev and Ops share ownership |
| Error budget | SLO compliance determines deployment speed |

## SLI / SLO / SLA

| Term | Definition | Example |
|------|------------|---------|
| SLI (Indicator) | Actual measured metric | Request latency P99 = 150ms |
| SLO (Objective) | Target reliability level | P99 latency < 200ms, 99.9% of time |
| SLA (Agreement) | Contract with consequences | 99.9% uptime or service credit |

## Error Budget

```
Error Budget = 100% - SLO

Example: 99.9% SLO → 0.1% error budget = ~43 minutes/month

When error budget remains → Can deploy new features
When error budget exhausted → Freeze deployments, focus on reliability
```

## Toil

| Characteristic | Example |
|----------------|---------|
| Manual | Restarting server by hand |
| Repetitive | Same incident response every week |
| Automatable | Could be scripted or eliminated |
| Tactical | No enduring value |
| Ops-heavy | No software engineering involved |

**SRE goal**: Keep toil under 50% of time.

## Key Practices

### Incident Management

```
Detection → Triage → Mitigation → Resolution → Postmortem
```

- Severity-based response (SEV-1: page, SEV-3: ticket)
- Well-defined escalation paths
- Blameless postmortems (focus on process, not people)

### Capacity Planning

```
Usage(t) = Current_usage × (1 + growth_rate)^t

Provision at 2x expected peak + lead time for new capacity
```

### Change Management

- Progressive rollouts (canary → 10% → 50% → 100%)
- Feature flags for instant rollback
- Automate testing and deployment
- Monitor error budget during rollout

## Production Readiness Checklist

| Area | Check |
|------|-------|
| Monitoring | SLIs defined and dashboards built |
| Alerting | Alerts are actionable (no false positives) |
| Backup | Data backed up, restore tested |
| Incident runbook | Steps documented and rehearsed |
| Capacity | Load tested to 2x expected peak |
| Security | Dependencies scanned, least privilege |
| Disaster recovery | Failover tested in production |

## SRE vs DevOps

| SRE | DevOps |
|-----|--------|
| Implementation of DevOps principles | Cultural/philosophical movement |
| Specific practices (error budgets, SLOs) | General practices (collaboration, automation) |
| Originated at Google | Community-driven |
| Operations is a software problem | Dev and Ops work together |

## Reliability Tradeoffs

| Sacrifice | To Gain |
|-----------|---------|
| New features | Stability (use error budget) |
| Performance | Consistency |
| Deployment speed | Safety (progressive rollout) |
| Cost | Capacity headroom |
| Simplicity | Fault tolerance (redundancy) |

## Firefighting vs Engineering

```
Time spent:
├── Firefighting (incidents, pages, manual fixes) → Reduce via automation
└── Engineering (automation, tools, architecture) → Increase

SRE target: > 50% engineering time
```

**Links**: [[Incident Response]] | [[Monitoring and Observability]] | [[Chaos Engineering]] | [[DevOps]] | [[CI CD Pipelines]]
