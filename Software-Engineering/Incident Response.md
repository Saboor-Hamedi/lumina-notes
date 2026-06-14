---
id: a1b2c3d4-1034-4000-8000-000000000034
title: Incident Response
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001034
---
# Incident Response

Incident response is the process of detecting, investigating, mitigating, and learning from production incidents.

## Severity Levels

| Level | Definition | Response Time | Example |
|-------|------------|---------------|---------|
| SEV-1 | Critical — service down | < 15 min | Entire site unreachable |
| SEV-2 | Major — degraded | < 1 hour | Slow queries, non-critical feature broken |
| SEV-3 | Minor — low impact | < 1 day | Cosmetic bug, non-urgent |
| SEV-4 | Negligible | Next sprint | Feature request, documentation |

## Incident Lifecycle

```
Detect → Triage → Respond → Resolve → Review
```

### 1. Detect

| Source | Method |
|--------|--------|
| Monitoring alerts | Prometheus, Datadog, Grafana |
| User reports | Support tickets, social media |
| Synthetic checks | Pingdom, Playwright e2e |
| Log analysis | Error rate spike |

### 2. Triage

- Is it a real incident or false alarm?
- What is the severity?
- Who is the right responder?
- Start a timeline

### 3. Respond

- **Stop the bleeding**: Mitigate (rollback, feature flag, scale up)
- **Communicate**: Post to incident channel, update status page
- **Investigate**: Logs, metrics, traces, recent deploys
- **Escalate**: If stuck, bring in additional expertise

### 4. Resolve

- Apply fix (hotfix deploy, config change)
- Verify fix in production
- Monitor for recurrence

### 5. Postmortem (Review)

Blameless postmortem focuses on process, not people:

| Section | Content |
|---------|---------|
| Summary | What happened in 2-3 sentences |
| Timeline | Key events with timestamps |
| Root cause | What triggered the incident |
| Impact | Users affected, revenue loss, time to resolve |
| Action items | Fixes with owners and deadlines |
| Lessons | What went well, what could improve |

## On-Call Best Practices

- Defined rotation schedule (primary + secondary)
- Alert fatigue → tune or suppress noisy alerts
- Runbooks for common scenarios
- Follow-the-sun model for global teams
- Guarantee time off after on-call week

## Incident Command System

| Role | Responsibility |
|------|----------------|
| Incident Commander | Coordinates response, makes decisions |
| Scribe | Records timeline, writes postmortem |
| Lead Responder | Investigates and resolves |
| Comms Lead | Updates stakeholders, status page |

## Prevention

- Chaos engineering (Gremlin, Chaos Monkey)
- Game days (practice incidents in staging)
- Feature flags for gradual rollouts
- Progressive delivery (canary → 10% → 50% → 100%)

**Links**: [[Monitoring and Observability]] | [[Chaos Engineering]] | [[CI CD Pipelines]] | [[Load Testing]] | [[Logging Best Practices]]
