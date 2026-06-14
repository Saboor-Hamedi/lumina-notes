---
id: a1b2c3d4-1123-4000-8000-000000000123
title: Estimation and Planning
language: markdown
tags: [software-engineering]
selection: null
isPinned: false
timestamp: 1781500001123
---
# Estimation and Planning

Estimation predicts effort for software work. Planning breaks work into actionable pieces. Both are essential for predictable delivery.

## Estimation Units

| Unit | Granularity | Used For | Accuracy |
|------|-------------|----------|----------|
| T-shirt sizes (XS-XXL) | Coarse | Roadmap, epics | ±50% |
| Story points (1, 2, 3, 5, 8, 13) | Medium | Sprint planning | ±25% |
| Ideal hours/days | Fine | Small tasks | ±15% |
| # of tasks | Coarse | Estimating unknown work | ±100% |

## Estimation Techniques

| Technique | How | Best For |
|-----------|-----|----------|
| Planning Poker | Team estimates together, discuss deviations | Sprint backlog |
| T-shirt sizing | Relative size comparison | Epics, large features |
| Three-point (PERT) | (Optimistic + 4×Most Likely + Pessimistic) / 6 | Task-level |
| Analogy | Compare to similar completed work | Well-understood features |
| Wideband Delphi | Multiple experts estimate independently, converge | High uncertainty |
| Affinity mapping | Sort items into size buckets | Backlog prioritization |

```python
# PERT estimate
def pert(optimistic, likely, pessimistic):
    return (optimistic + 4 * likely + pessimistic) / 6

def std_dev(optimistic, pessimistic):
    return (pessimistic - optimistic) / 6

# Example: feature will take 3-8 days, most likely 5
estimate = pert(3, 5, 8)  # ~5.2 days
```

## Story Points vs Hours

| Story Points | Hours |
|-------------|-------|
| Relative, not absolute | Absolute |
| Abstract (no unit degradation) | Degrade over time (velocity changes) |
| Team-specific | Universal |
| Accounts for complexity + uncertainty | Pure time estimate |

## Velocity-Based Planning

```python
# Use historical velocity to predict future delivery
velocities = [28, 32, 25, 30, 35, 28]  # story points per sprint
avg_velocity = sum(velocities) / len(velocities)  # ~30

# How many sprints for 150-point feature?
sprints_needed = 150 / avg_velocity  # ~5 sprints

# Use average (or median for stability)
# Add buffer: 80th percentile of historical velocity
```

## The Cone of Uncertainty

```
Initial concept: 4x uncertainty
  ↓
Approved: 2x
  ↓
Requirements specified: 1.5x
  ↓
Design complete: 1.25x
  ↓
Coding complete: 1.1x
```

Early estimates are inherently uncertain — refine as you learn more.

## Common Estimation Traps

| Trap | Mitigation |
|------|------------|
| Anchoring bias | First estimate mentioned anchors the group |
| Planning fallacy | Assume everything goes right — use reference class |
| Optimism bias | Ask "worst case" in addition to "best case" |
| Overconfidence | Range estimates, not point estimates |
| Unknown unknowns | Dedicated spike/investigation task |
| Sandbagging | Estimate based on objective data, not fear |

## Sprint Planning

```
Sprint capacity = Team size × Sprint days × Focus factor

Example:
4 devs × 10 days × 0.7 focus factor = 28 ideal days

Select backlog items totaling ≤ capacity
```

## Rolling Wave Planning

Plan near-term in detail, future at higher level:

```
Sprint 1: Detailed (days)
Sprint 2-3: Medium (story points)  
Sprint 4-6: Coarse (t-shirt sizes)
Beyond: Epic-level
```

## Risk-Adjusted Estimation

```python
# Monte Carlo simulation
import random

def monte_carlo_estimate(tasks, num_simulations=10000):
    results = []
    for _ in range(num_simulations):
        total = 0
        for task in tasks:
            # Sample from triangular distribution
            total += random.triangular(
                task["optimistic"], task["pessimistic"], task["likely"]
            )
        results.append(total)

    return {
        "p50": sorted(results)[len(results) // 2],
        "p80": sorted(results)[len(results) * 8 // 10],
        "p95": sorted(results)[len(results) * 95 // 100],
    }

# Use p80 for commitment, p50 for expectation
```

**Links**: [[Developer Workflow Automation]] | [[Agile Development]] | [[Code Review Process]] | [[Technical Debt Management]] | [[Developer Velocity]]
