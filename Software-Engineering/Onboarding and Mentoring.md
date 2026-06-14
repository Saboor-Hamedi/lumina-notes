---
id: a1b2c3d4-1124-4000-8000-000000000124
title: Onboarding and Mentoring
language: markdown
tags: [software-engineering]
selection: null
isPinned: false
timestamp: 1781500001124
---
# Onboarding and Mentoring

Effective onboarding and mentoring accelerate new team members to productivity and build a culture of learning and growth.

## The Onboarding Funnel

```
Day 1: Setup & Context → Week 1: Small tasks → Month 1: Own features → Month 3: Autonomous
```

| Phase | Duration | Goal | Success Metric |
|-------|----------|------|----------------|
| Logistics | Day 1-2 | Access, tools, accounts | Can open all tools |
| Environment | Day 2-5 | Local dev running, first PR | First merged PR |
| Context | Week 1-2 | Understand domain, architecture | Explains system overview |
| Contribution | Week 2-4 | Shipping features with guidance | 2+ merged features |
| Autonomy | Month 1-3 | Independent contributions | Code reviews others' PRs |

## Pre-Onboarding Checklist

- [ ] Laptop ordered and configured
- [ ] All accounts provisioned (email, GitHub, Slack, CI)
- [ ] Dev environment documented (README, setup script)
- [ ] First week schedule planned
- [ ] Buddy assigned (not the manager)
- [ ] Desk/seat assigned
- [ ] Onboarding project defined (small, achievable)

## Day 1 Structure

```
09:00 - Welcome, team intros
10:00 - Setup (with buddy)
12:00 - Lunch with team
13:00 - Dev environment walkthrough
14:00 - First commit (doc fix, test, trivial bug)
15:00 - Architecture overview (whiteboard)
16:00 - Set up monitoring/dashboards access
17:00 - Pair with buddy on a real ticket
```

## Buddy System

| Role | Buddy (Peer) | Manager |
|------|-------------|---------|
| Focus | Technical, cultural, day-to-day | Career, performance, strategy |
| Meetings | Daily standup buddy time, lunch | 1:1s weekly |
| Questions | "How do I deploy?", "What's the PR process?" | "How am I doing?", "Career growth" |
| Duration | First 2-4 weeks | Ongoing |

## Mentoring

### Types of Mentoring

| Type | Format | Best For |
|------|--------|----------|
| Formal | Structured program with goals | Career growth |
| Informal | Organic, need-based | Technical skills |
| Reverse | Junior teaches senior | New tech, fresh perspective |
| Peer | Same-level knowledge share | Cross-team learning |

### Effective Mentoring Techniques

- **Pair programming**: Strong learning transfer (real-time feedback)
- **Code review coaching**: Explain why, not just what
- **Design doc review**: Discuss tradeoffs before implementation
- **Learning projects**: Small project outside main work for skill building
- **Socratic method**: Ask questions instead of giving answers

## Pair Programming Styles

| Style | How | When |
|-------|-----|------|
| Driver/Navigator | One types, one guides | Complex features, mentoring |
| Ping-pong | Write test, implement, swap | TDD, learning codebase |
| Strong-style | "Let's try..." | Exploration, debugging |
| Tour-style | Expert leads through codebase | Onboarding, codebase tour |

## Feedback Framework (SBI)

| Step | Action |
|------|--------|
| Situation | "In yesterday's standup..." |
| Behavior | "...you interrupted three times..." |
| Impact | "...which made it hard for others to share." |

## Ramp-Up Plan Template

```markdown
## Week 1: Foundation
- [ ] Read architecture docs
- [ ] Set up local environment
- [ ] Complete 3 small PRs
- [ ] Pair on deployment process

## Week 2-3: Feature
- [ ] Take ownership of a small feature
- [ ] Write design doc for review
- [ ] Present sprint demo

## Month 2: Independence
- [ ] Review 5+ PRs from others
- [ ] Lead a feature from design to release
- [ ] On-call shadow + first solo shift

## Month 3: Contribution
- [ ] Propose improvement to dev processes
- [ ] Mentor a new hire (if available)
```

## Measuring Onboarding Success

| Metric | Target | When |
|--------|--------|------|
| Time to first PR | < 3 days | Week 1 |
| Time to first production PR | < 2 weeks | Week 2 |
| PRs merged per week | Match team average | Month 3 |
| Comfort level survey | > 4/5 | Monthly |
| Buddy feedback | Positive | End of first month |

**Links**: [[Code Review Process]] | [[Developer Workflow Automation]] | [[Dev Environment Setup]] | [[Developer Experience]] | [[Programming Resources]]
