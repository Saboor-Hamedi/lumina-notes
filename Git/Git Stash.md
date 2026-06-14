---
id: git-019-0000-0000-0000-000000000019
title: Git Stash
language: markdown
tags: [git, stash]
selection: null
isPinned: false
timestamp: 1781500000019
---
# Git Stash

**Links**: [[Git Add and Status]] | [[Git Checkout and Switch]] | [[Git Rebase]] | [[Git Clean]] | [[Git Worktrees]]

## What is Stashing?

Stashing temporarily shelves your changes so you can work on something else, then reapply them later.

## Basic Operations

```bash
# Stash current changes (tracked files only)
git stash

# Stash including untracked files
git stash -u       # or --include-untracked

# Stash with a message
git stash push -m "WIP: login form validation"

# List stashes
git stash list

# Show what's in a stash
git stash show -p stash@{0}
```

## Applying Stashes

```bash
# Apply most recent stash (keeps stash)
git stash apply

# Apply and remove stash
git stash pop

# Apply a specific stash
git stash apply stash@{2}

# Pop a specific stash
git stash pop stash@{2}
```

## Advanced Stashing

```bash
# Stash only specific files
git stash push -m "partial stash" src/file1.ts src/file2.ts

# Stash staged changes separately
git stash push --staged -m "only staged"

# Create a branch from a stash
git stash branch new-branch stash@{0}

# Remove a stash
git stash drop stash@{0}

# Clear all stashes
git stash clear
```

## Use Cases

| Scenario | Command |
|----------|---------|
| Need to switch branches urgently | `git stash` → switch → `git stash pop` |
| Pull/rebase with dirty tree | `git stash` → pull → `git stash pop` |
| Experiment that didn't work | `git stash drop` |
| Start fresh | `git stash clear` |

**Next**: [[Git Reset]] — Reset HEAD and staging area