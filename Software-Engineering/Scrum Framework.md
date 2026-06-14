---
id: a1b2c3d4-1206-4000-8000-000000000206
title: Scrum Framework
language: markdown
tags: [software-engineering]
selection: null
isPinned: false
timestamp: 1781500001206
---
# Scrum Framework

Scrum is an agile framework for managing complex projects. It emphasizes iterative delivery, inspection, and adaptation.

## Roles

| Role | Responsibility |
|------|---------------|
| Product Owner | Maximizes product value, manages backlog, defines user stories |
| Scrum Master | Facilitates Scrum process, removes impediments, coaches team |
| Developers | Self-organizing team that delivers increments |

## Events

```
Sprint (2-4 weeks)
  ├── Sprint Planning (max 2 hours/week of sprint)
  │     Outcome: Sprint Goal, Sprint Backlog
  │
  ├── Daily Scrum (15 minutes)
  │     Each developer: What did I do? What will I do? Any blockers?
  │
  ├── Sprint Review (max 1 hour/week of sprint)
  │     Demo completed work to stakeholders
  │
  └── Sprint Retrospective (max 45 min/week of sprint)
        Inspect and adapt the process
```

## Artifacts

| Artifact | Description | Who Owns It |
|----------|-------------|-------------|
| Product Backlog | Ordered list of everything needed | Product Owner |
| Sprint Backlog | Selected items + plan for the sprint | Developers |
| Increment | Sum of all completed Product Backlog items | Developers |

## User Stories

```yaml
user_story:
  format: "As a [role], I want [goal] so that [reason]"
  example: "As a customer, I want to track my order so that I know when it arrives"

  acceptance_criteria:
    - "Customer can enter order number on tracking page"
    - "System displays current order status"
    - "System shows estimated delivery date"
    - "System shows tracking history"

  estimation: 5 story points
  priority: high
```

## Estimation

```markdown
# Common estimation techniques:

Planning Poker:
  - Team estimates using Fibonacci sequence (1, 2, 3, 5, 8, 13, 21)
  - Each member selects card simultaneously
  - Discuss differences, re-vote until consensus

T-Shirt Sizing:
  - XS, S, M, L, XL, XXL (relative sizing)
  - Convert to story points for velocity tracking

Affinity Estimation:
  - Sort stories by relative size
  - Group into buckets (small, medium, large)
```

## Sprint Planning

```
Inputs:
  - Product Backlog (refined, estimated, prioritized)
  - Team velocity (historical)
  - Capacity (vacation, meetings)

Outputs:
  - Sprint Goal (one sentence objective)
  - Sprint Backlog (selected stories + plan)

Activities:
  Part 1: What can we do? (select stories based on velocity)
  Part 2: How will we do it? (break into tasks, assign ownership)
```

## Daily Scrum

```markdown
Format (15 minutes, standing):
  1. What did I accomplish yesterday?
  2. What will I do today?
  3. Are there any blockers?

Focus: Inspect progress toward Sprint Goal, adjust plan.
Not: Status report for management.
```

## Definition of Done

```markdown
Definition of Done (shared standard):
  [x] Code written
  [x] Code reviewed
  [x] Unit tests passing
  [x] Integration tests passing
  [x] Acceptance criteria met
  [x] Documentation updated
  [x] Deployed to staging
  [x] Product Owner approved
```

## Sprint Review

```markdown
Format:
  1. Product Owner reviews what was completed vs backlog
  2. Developers demo working software
  3. Stakeholders provide feedback
  4. Product Owner updates backlog based on feedback
  5. Review timeline, budget, market changes

Outcome: Updated Product Backlog with new insights.
```

## Sprint Retrospective

```markdown
Format (45 min - 3 hours):
  1. Set the stage (5 min)
  2. Gather data — what went well, what could improve (10-15 min)
  3. Generate insights — identify root causes (10-15 min)  
  4. Decide what to do — actionable improvements (10-15 min)
  5. Close retrospective (5 min)

Common formats:
  - Start/Stop/Continue
  - Sailboat (wind + anchors)
  - 4Ls (Liked, Learned, Lacked, Longed For)
  - Mad/Sad/Glad
```

## Scrum Board

```markdown
Columns:
  Backlog | To Do | In Progress | Review | Done

  Each story moves left to right.
  Tasks (not stories) are in "In Progress".
  Only one person per task (avoid multi-tasking).
```

## Scrum Metrics

| Metric | Description | Target |
|--------|-------------|--------|
| Velocity | Story points completed per sprint | Stable trend |
| Sprint Burndown | Remaining work vs time | On track to zero |
| Release Burnup | Completed vs total work | Approaching release |
| Cycle Time | Time from start to completion | Decreasing |
| Lead Time | Time from request to delivery | Decreasing |

## Common Pitfalls

| Pitfall | Solution |
|---------|----------|
| No PO availability | Dedicated PO, set office hours |
| Scrum master as manager | It's a servant-leader role, not manager |
| Changing sprint scope | Only if team agrees and scope is removed |
| No Definition of Done | Create and enforce it |
| Skipping retro | Most important ceremony for improvement |
| Velocity as productivity | It's a planning tool, not a performance metric |
| Too many meetings | Timebox strictly |
