---
id: git-013-0000-0000-0000-000000000013
title: Git Rebase
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000013
---
# Git Rebase

**Links**: [[Git Branch]] | [[Git Merge]] | [[Git Interactive Rebase]] | [[Git Conflict Resolution]] | [[Git Advanced Merging]] | [[Git Workflows]]

## What is Rebasing?

Rebasing reapplies commits from one branch onto the tip of another, creating a **linear history** instead of merge commits.

```
Before rebase:
main:     a1b2c3d ← f1g2h3i
feature:  a1b2c3d ← j4k5l6m

After rebase (git rebase main on feature):
feature:  a1b2c3d ← f1g2h3i ← j4k5l6m'  (new hashes!)
```

## Merge vs Rebase

| Aspect | Merge | Rebase |
|--------|-------|--------|
| History | Preserves branching | Linear, cleaner |
| Safety | No history rewriting | Rewrites commit hashes |
| Conflicts | Resolved once | Resolved per commit |
| Shared branches | Safe | **DANGEROUS** |

## Basic Rebase

```bash
# While on feature branch
git rebase main

# Rebase onto a different branch
git rebase --onto main feature-branch topic-branch
```

## The Golden Rule

**Never rebase commits that have been pushed to a shared repository.**

Rebasing rewrites history — anyone who pulled your old commits will have diverging history.

## Rebase with Pull

```bash
# Pull and rebase instead of merge
git pull --rebase

# Set as default
git config --global pull.rebase true
```

## Abort a Rebase

```bash
# If things go wrong
git rebase --abort
```

**Next**: [[Git Interactive Rebase]] — Squash, reword, reorder commits