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

**Links**: [[Branch]] | [[Merge]] | [[Rebase]] | [[Worktrees]] | [[Restore]] | [[Stash]]

Moving between branches and restoring files are fundamental Git operations. Git provides two commands — checkout (traditional, multi-purpose) and switch (modern, focused) — for navigating your repository.

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

# Switch to remote branch (creates local tracking)
git switch origin/feature
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

# Restore with staging and working tree
git restore --staged --worktree file.txt
```

## Command Mapping (Legacy → Modern)

| Legacy (checkout) | Modern Equivalent |
|-------------------|-------------------|
| `git checkout branch` | `git switch branch` |
| `git checkout -b new` | `git switch -c new` |
| `git checkout -- file` | `git restore file` |
| `git checkout HEAD file` | `git restore --source HEAD file` |

## Detached HEAD

When you check out a specific commit (not a branch), HEAD points directly to a commit:
```
HEAD → a1b2c3d (not on any branch)
```
- Changes can be lost unless you create a branch
- Create one: `git switch -c new-branch`

## Common Patterns

| Action | Command |
|--------|---------|
| Switch to feature | `git switch feature` |
| Create new branch | `git switch -c feature/user-auth` |
| Go back to previous branch | `git switch -` |
| Discard file changes | `git restore file.txt` |
| Unstage file | `git restore --staged file.txt` |

**Next**: [[Merge]] — Combine branches
