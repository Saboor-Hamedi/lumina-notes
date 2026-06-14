---
id: git-011-0000-0000-0000-000000000011
title: Git Merge
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000011
---
# Git Merge

**Links**: [[Git Branch]] | [[Git Checkout and Switch]] | [[Git Conflict Resolution]] | [[Git Rebase]] | [[Git Advanced Merging]] | [[Git Workflows]]

## What is Merging?

Merging combines changes from one branch into another. Git finds a common ancestor and applies the differences.

## Fast-Forward Merge

When the target branch hasn't diverged, Git just moves the pointer forward:

```
Before:  main → a1b2c3d
         feature → e4f5g6h (ahead of main)

After:   main → e4f5g6h (fast-forward)
```

```bash
git checkout main
git merge feature    # Fast-forwards main to feature
```

## Three-Way Merge

When branches have diverged, Git creates a merge commit with two parents:

```
Before:
main:     a1b2c3d → f1g2h3i
feature:  a1b2c3d → j4k5l6m

After:
main:     a1b2c3d → f1g2h3i → merge commit
feature:  a1b2c3d → j4k5l6m ↗
```

```bash
git checkout main
git merge feature    # Creates merge commit
```

## Merge Strategies

| Strategy | When | Behavior |
|----------|------|----------|
| `recursive` | Default for 2 branches | Three-way merge |
| `ort` | New default (Git 2.33+) | Faster, better conflict handling |
| `octopus` | 3+ branches | Multi-way merge |
| `ours` | Any | Keep our version entirely |
| `subtree` | Subproject merge | Merge with subtree adjustment |

## Options

```bash
# Force merge commit even if fast-forward possible
git merge --no-ff feature

# Squash merge (all changes as one commit)
git merge --squash feature

# Only merge if fast-forward possible
git merge --ff-only feature
```

**Next**: [[Git Conflict Resolution]] — Handle merge conflicts