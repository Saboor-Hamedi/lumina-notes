---
id: git-020-0000-0000-0000-000000000020
title: Git Reset
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000020
---
# Git Reset

**Links**: [[Git Commit]] | [[Git Revert]] | [[Git Restore]] | [[Git Clean]] | [[Git Reflog]] | [[Git Checkout and Switch]]

## What Does Reset Do?

`git reset` moves the current branch HEAD and optionally updates the staging area and working tree.

## The Three Modes

```bash
# SOFT: Move HEAD only, keep staged and working changes
git reset --soft HEAD~1

# MIXED (default): Move HEAD + unstage, keep working changes
git reset HEAD~1

# HARD: Move HEAD + unstage + discard working changes
git reset --hard HEAD~1
```

| Mode | HEAD | Staging (Index) | Working Tree |
|------|------|-----------------|--------------|
| `--soft` | Moves | Unchanged | Unchanged |
| `--mixed` | Moves | Reset | Unchanged |
| `--hard` | Moves | Reset | Reset |

## Common Patterns

```bash
# Undo last commit, keep changes staged
git reset --soft HEAD~1

# Undo last commit, unstage but keep changes
git reset HEAD~1

# Throw away last commit AND changes
git reset --hard HEAD~1

# Unstage a file (same as git restore --staged)
git reset HEAD file.txt

# Reset to a specific commit
git reset --hard a1b2c3d
```

## Going Back Multiple Commits

```bash
# Go back 3 commits
git reset --hard HEAD~3

# Go back to commit hash
git reset --hard a1b2c3d
```

## DANGER: --hard

`git reset --hard` discards working tree changes permanently. Unstaged, uncommitted changes are **gone**.

Safe alternative: `git stash` before resetting.

**Next**: [[Git Revert]] — Safe undo with new commits