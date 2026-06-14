---
id: git-022-0000-0000-0000-000000000022
title: Git Restore
language: markdown
tags: [git, restore]
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

# Restore from staging area (same as above)
git restore --worktree file.txt
```

## Restore from a Specific Commit

```bash
# Restore file as it was in a specific commit
git restore --source a1b2c3d file.txt

# Restore from two commits ago
git restore --source HEAD~2 file.txt

# Restore all files from a specific commit
git restore --source a1b2c3d .

# Restore from a tag
git restore --source v1.0.0 file.txt
```

## Unstage Files

Remove files from staging area without changing working tree.

```bash
# Unstage a file
git restore --staged file.txt

# Unstage all files
git restore --staged .

# Unstage and restore to HEAD
git restore --source HEAD --staged --worktree file.txt
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

# Restore with exclusion
git restore . -- ':!node_modules/'
```

## Restore Options Table

| Flag | Effect |
|------|--------|
| `--staged` | Restore staging area (unstage) |
| `--worktree` | Restore working tree (default) |
| `--source <ref>` | Restore from specific commit |
| `--ours` | Keep current branch version |
| `--theirs` | Keep merge branch version |
| `-p` | Interactive (choose hunks) |

**Next**: [[Git Clean]] — Remove untracked files
