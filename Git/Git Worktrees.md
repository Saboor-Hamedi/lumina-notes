---
id: git-029-0000-0000-0000-000000000029
title: Git Worktrees
language: markdown
tags: [git, worktrees]
selection: null
isPinned: false
timestamp: 1781500000029
---
# Git Worktrees

**Links**: [[Git Branch]] | [[Git Checkout and Switch]] | [[Git Stash]] | [[Git Submodules]]

## What are Worktrees?

Worktrees allow you to check out multiple branches simultaneously in separate directories, all sharing the same `.git` directory.

## Why Use Worktrees?

| Scenario | Without Worktree | With Worktree |
|----------|-----------------|---------------|
| Fix a bug while working on feature | Stash/commit + switch | Open second directory |
| Review a PR | Clone repo again | `git worktree add` |
| Compare branches side by side | Multiple clones | Two worktrees |

## Basic Usage

```bash
# Create a worktree for a branch
git worktree add ../project-hotfix hotfix

# Create worktree at specific commit
git worktree add ../project-v2 a1b2c3d

# Create worktree and new branch
git worktree add -b new-feature ../project-feature main
```

## Managing Worktrees

```bash
# List worktrees
git worktree list

# Output:
# /path/to/main-project  a1b2c3d [main]
# /path/to/hotfix        e4f5g6h [hotfix]
```

## Removing Worktrees

```bash
# Remove worktree (after done)
git worktree remove ../project-hotfix

# Force remove
git worktree remove --force ../project-hotfix

# Prune stale worktree references
git worktree prune
```

## Key Facts

- Each worktree is a complete working directory
- You can have unlimited worktrees
- A branch can only be checked out in one worktree
- Worktrees share objects and refs (no duplication)
- The `.git` file in a worktree points to the main repo

**Next**: [[Git Hooks]] — Client-side automation