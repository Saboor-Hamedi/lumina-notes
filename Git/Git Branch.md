---
id: git-009-0000-0000-0000-000000000009
title: Git Branch
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000009
---
# Git Branch

**Links**: [[Git Overview]] | [[Git Commit]] | [[Git Checkout and Switch]] | [[Git Merge]] | [[Git Rebase]] | [[Git Workflows]] | [[Git Remote]]

## What is a Branch?

A branch is a **lightweight movable pointer** to a commit. Creating a branch is instantaneous — Git just writes a 41-byte file with a hash and name.

```
main:  a1b2c3d ← HEAD
         ↓
feature: a1b2c3d   (before commit)

main:  a1b2c3d ← HEAD
         ↓
feature: e4f5g6h   (after commit on feature)
```

## Operations

```bash
# List branches
git branch                          # Local
git branch -r                       # Remote
git branch -a                       # All

# Create
git branch feature-login
git branch feature-login a1b2c3d    # From specific commit

# Delete
git branch -d feature-login         # Safe (prevents loss)
git branch -D feature-login         # Force delete

# Rename
git branch -m old-name new-name

# Show merged/unmerged
git branch --merged                 # Already merged
git branch --no-merged              # Not merged
```

## Naming Conventions

- `main` or `master` — Default primary branch
- `feature/` — New features (`feature/user-auth`)
- `bugfix/` — Bug fixes (`bugfix/login-crash`)
- `release/` — Release branches (`release/v2.1`)
- `hotfix/` — Urgent production fixes (`hotfix/security-patch`)

**Next**: [[Git Checkout and Switch]] — Navigate branches