---
id: git-021-0000-0000-0000-000000000021
title: Git Revert
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000021
---
# Git Revert

**Links**: [[Git Commit]] | [[Git Reset]] | [[Git Cherry-Pick]] | [[Git Reflog]] | [[Git Advanced Merging]]

## What is Revert?

`git revert` creates a **new commit** that undoes changes from a previous commit. Unlike reset, it doesn't rewrite history — it's safe for shared branches.

## Basic Usage

```bash
# Revert a single commit
git revert a1b2c3d

# Revert without opening editor (auto-commit)
git revert --no-edit a1b2c3d

# Revert multiple commits (in order, newest first)
git revert a1b2c3d..e4f5g6h
```

## Revert vs Reset

| Aspect | `git reset` | `git revert` |
|--------|-------------|--------------|
| History | Rewrites (removes commits) | Adds new commit (preserves history) |
| Safety | Dangerous on shared branches | Safe on shared branches |
| Effect | Moves branch pointer | Creates inverse changes |
| Published | Never on public branches | Safe anytime |

## Advanced Revert

```bash
# Revert but don't commit (let you edit)
git revert --no-commit a1b2c3d

# Revert a range
git revert HEAD~3..HEAD

# Revert a merge commit (needs -m flag)
git revert -m 1 merge-commit-hash
```

## Revert a Merge

Merges have two parents. The `-m` flag specifies which parent to keep:

```bash
# -m 1 keeps the branch you were on
# -m 2 keeps the branch you merged in
git revert -m 1 a1b2c3d
```

**Next**: [[Git Restore]] — Restore files to previous states