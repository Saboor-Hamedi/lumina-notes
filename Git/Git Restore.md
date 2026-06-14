---
id: git-022-0000-0000-0000-000000000022
title: Git Restore
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000022
---
# Git Restore

**Links**: [[Git Add and Status]] | [[Git Checkout and Switch]] | [[Git Reset]] | [[Git Clean]] | [[Git Diff]]

## What is Restore?

`git restore` (introduced in Git 2.23) separates file restoration from branch switching. It handles undoing changes to files.

## Restore Working Tree

Discard uncommitted changes in your working directory.

```bash
# Discard changes to a file
git restore file.txt

# Discard changes to all files
git restore .

# Discard with caution
git restore --source HEAD file.txt
```

## Restore from a Specific Commit

```bash
# Restore file as it was in a specific commit
git restore --source a1b2c3d file.txt

# Restore from two commits ago
git restore --source HEAD~2 file.txt
```

## Unstage Files

Remove files from staging area without changing working tree.

```bash
# Unstage a file
git restore --staged file.txt

# Unstage all files
git restore --staged .
```

## Combined

```bash
# Unstage AND restore working tree to HEAD
git restore --source HEAD --staged --worktree file.txt

# Shorthand for unstage: git reset HEAD file.txt
# Shorthand for discard:  git checkout -- file.txt
```

## Restore Multiple Files

```bash
# Restore all .py files
git restore '*.py'

# Restore entire directory
git restore src/
```

**Next**: [[Git Clean]] — Remove untracked files