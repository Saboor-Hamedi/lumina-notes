---
id: git-010-0000-0000-0000-000000000010
title: Git Checkout and Switch
language: markdown
tags: [git, branching]
selection: null
isPinned: false
timestamp: 1781500000010
---
# Git Checkout and Switch

**Links**: [[Git Branch]] | [[Git Merge]] | [[Git Rebase]] | [[Git Worktrees]] | [[Git Restore]] | [[Git Stash]]

## git checkout (Traditional)

```bash
# Switch to a branch
git checkout feature-login

# Create and switch
git checkout -b new-feature

# Checkout a specific commit (detached HEAD)
git checkout a1b2c3d

# Restore a file to last committed state
git checkout -- file.txt
```

## git switch (Modern, Git 2.23+)

Separates branch switching from file restoration.

```bash
# Switch to branch
git switch feature-login

# Create and switch
git switch -c new-feature

# Return to previous branch
git switch -
```

## git restore (Modern)

For file restoration (replaces `git checkout -- file`).

```bash
# Restore working tree file
git restore file.txt

# Unstage a file
git restore --staged file.txt

# Restore from a specific commit
git restore --source a1b2c3d file.txt
```

## Detached HEAD

When you check out a specific commit (not a branch), HEAD points directly to a commit:
```
HEAD → a1b2c3d (not on any branch)
```
- Changes can be lost unless you create a branch
- Create one: `git switch -c new-branch`

**Next**: [[Git Merge]] — Combine branches