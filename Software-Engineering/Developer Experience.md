---
id: a1b2c3d4-1125-4000-8000-000000000125
title: Developer Experience
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001125
---
# Developer Experience

Developer Experience (DX) is the quality of interactions developers have with tools, APIs, frameworks, and internal platforms. Good DX reduces friction and boosts productivity.

## Pillars of DX

| Pillar | Description | Anti-Pattern |
|--------|-------------|--------------|
| Speed | Fast feedback loops | 10 min CI, slow local builds |
| Simplicity | Few steps, clear docs | 50-step setup guide |
| Consistency | Same patterns everywhere | Every team uses different tools |
| Reliability | Works predictably | Flaky tests, random failures |
| Discoverability | Easy to find what you need | No index, buried in wiki |

## Developer Productivity Metrics (SPACE)

| Metric | What It Measures |
|--------|-----------------|
| Satisfaction | How developers feel about their work |
| Performance | Quality of outcomes, not just output |
| Activity | PRs merged, commits, deploys |
| Communication | Collaboration effectiveness |
| Efficiency | Time spent on value vs overhead |

## Dev Environment Speed

| Action | Slow | Fast |
|--------|------|------|
| Clone + setup | 30 min (manual) | 2 min (scripted/devcontainer) |
| Build | 5 min | 10 sec (incremental) |
| Test run | 10 min | 30 sec (focused) |
| Deploy to preview | 20 min | 2 min |
| Local feedback | Manual refresh | Hot reload |

## Local Development Tools

| Tool | Purpose |
|------|---------|
| Devcontainers | Reproducible dev environments |
| Docker Compose | Local service dependencies |
| Tilt / Skaffold | Kubernetes development |
| Nodemon / watchers | Hot reload on file changes |
| Makefile / Taskfile | Standardized commands |

```makefile
# Makefile — one command to get started
.PHONY: setup test lint deploy

setup:  ## Full environment setup
	docker compose up -d
	npm install
	npx prisma migrate dev

test:   ## Run tests with watch mode
	npm run test:watch

lint:   ## Lint and format
	npm run lint && npm run format

deploy: ## Deploy to dev
	kubectl apply -f k8s/dev/
```

## API / SDK Design

| Principle | Good | Bad |
|-----------|------|-----|
| Consistent | `createUser()`, `getUser()`, `deleteUser()` | `addUser()`, `fetchProfile()`, `removeAccount()` |
| Predictable | Same error format everywhere | Inconsistent error structures |
| Self-documenting | Clear parameter names | `opts`, `args`, `data` |
| Minimal | Few required params | 20 required params |
| Error messages | "Invalid email: missing @" | "ValidationError: code 43" |

## Developer Portal (Backstage)

Internal developer portals:

| Feature | Benefit |
|---------|---------|
| Service catalog | Find any service, docs, and ownership |
| Software templates | Scaffold new services with standards |
| Tech docs | Centralized, service-linked documentation |
| API marketplace | Discover and consume internal APIs |
| Scorecards | Track standards compliance |

## Internal Platform Engineering

Build a platform team that creates paved roads:

```
Developer → Platform API → Abstracted infra (no tickets, no manual steps)
```

| Paved Road | Developer Experience |
|------------|---------------------|
| CI/CD pipeline | "Create file → auto-deployed" |
| Service deployment | "kubectl apply" or CLI one-liner |
| Database provisioning | "Request via portal, ready in 5 min" |
| Secrets management | "Inject via platform, never see them" |
| Monitoring | "Auto-instrumented, dashboards by default" |

## Documentation Experience

| Type | Format | Location |
|------|--------|----------|
| README | Point of entry | Repo root |
| API docs | Generated from code | dev.example.com/api |
| Runbooks | Operations procedures | Embedded in repos |
| ADRs | Architecture decisions | docs/adr/ |
| How-to guides | Step-by-step | Wiki or docs site |

## Developer Survey Questions

| Category | Questions |
|----------|-----------|
| Speed | "How long from idea to production?" |
| Quality | "How confident are you in your deployments?" |
| Tooling | "Do your tools help or hinder?" |
| Culture | "Do you feel psychological safety?" |
| Platform | "Can you self-serve infrastructure?" |

## Reducing Friction Checklist

- [ ] Can a new developer ship a change on day 1?
- [ ] Is there a single `make setup` or equivalent?
- [ ] Are CI times < 5 minutes for most checks?
- [ ] Is local development possible without internet access?
- [ ] Are there runbooks for common incidents?
- [ ] Are API changes versioned and backward compatible?
- [ ] Can developers self-serve infrastructure?
- [ ] Is there a clear escalation path for blocked developers?

**Links**: [[Dev Environment Setup]] | [[Onboarding and Mentoring]] | [[Developer Workflow Automation]] | [[Command Line Productivity]] | [[CI CD Pipelines]]
