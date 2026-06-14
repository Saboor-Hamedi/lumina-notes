---
id: git-021-0000-0000-0000-000000000021
title: Git Revert
language: markdown
tags: [git, revert]
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

# Revert the most recent commit
git revert HEAD
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

# Revert multiple specific commits
git revert a1b2c3d e4f5g6h j4k5l6m
```

## Revert a Merge

Merges have two parents. The `-m` flag specifies which parent to keep:

```bash
# -m 1 keeps the branch you were on
# -m 2 keeps the branch you merged in
git revert -m 1 a1b2c3d
```

## Handling Revert Conflicts

```bash
# If revert causes conflicts:
# 1. Resolve conflicts manually
# 2. Stage the resolved files
git add .

# 3. Continue the revert
git revert --continue

# Or abort entirely
git revert --abort
```

## Practical Scenarios

| Scenario | Command |
|----------|---------|
| Undo a buggy deploy commit | `git revert a1b2c3d` |
| Roll back a feature from last week | `git revert OLDER_COMMIT` |
| Undo a merge on main | `git revert -m 1 MERGE_HASH` |
| Revert but keep changes for editing | `git revert --no-commit a1b2c3d` |

```mermaid
flowchart LR
    A[Before] --> B[C1 - C2 - C3 - C4]
    B --> C[git revert C3]
    C --> D[C1 - C2 - C3 - C4 - C3']
    D --> E[C3' undoes C3, history preserved]
```

**Next**: [[Git Restore]] — Restore files to previous states
