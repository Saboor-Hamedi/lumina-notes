---
id: git-016-0000-0000-0000-000000000016
title: Git Push
language: markdown
tags: [git, remote]
selection: null
isPinned: false
timestamp: 1781500000016
---
# Git Push

**Links**: [[Remote]] | [[Pull and Fetch]] | [[Branch]] | [[Rebase]] | [[Workflows]] | [[Reflog]]

Pushing sends your local commits to a remote repository, making them available to collaborators. It is the primary mechanism for publishing and sharing work.

## Basic Push

```bash
# Push to origin/main
git push origin main

# Push current branch to matching remote branch
git push

# Push and set upstream (-u sets tracking)
git push -u origin feature

# Push to a different remote branch name
git push origin feature:feature-v2
```

## Push Strategies

```bash
# Push all local branches to remote
git push origin --all

# Push tags
git push origin --tags

# Delete a remote branch
git push origin --delete feature-old

# Push specific commit (creates remote branch at that commit)
git push origin a1b2c3d:refs/heads/new-branch

# Push and set upstream shorthand
git push -u origin HEAD
```

## Force Push

Rewrites remote history — use with extreme caution.

```bash
# DANGEROUS: overwrites remote
git push --force

# SAFER: rejects if remote has new commits
git push --force-with-lease

# EVEN SAFER: specify expected upstream ref
git push --force-if-includes
```

## Force Push Safety Table

| Command | Checks Remote Has New Commits? | Recommended? |
|---------|-------------------------------|--------------|
| `--force` | No | ❌ Never on shared branches |
| `--force-with-lease` | Yes | ✅ On personal branches |
| `--force-if-includes` | Yes (stricter) | ✅ Best option |

## Push Rejected?

```bash
# Remote has commits you don't have
# Solution: pull/rebase first, then push
git pull --rebase origin main
git push origin main
```

## Configure Push Default

```bash
# Only push current branch (safer)
git config --global push.default current

# Push all matching branches
git config --global push.default matching
```

## Common Push Scenarios

| Scenario | Command |
|----------|---------|
| First push of new branch | `git push -u origin feature` |
| Update feature branch | `git push` |
| Delete remote branch | `git push origin --delete old-branch` |
| Push tags for release | `git push origin --tags` |
| Push to different remote | `git push upstream feature` |
| Recover with force after rebase | `git push --force-with-lease` |

**Next**: [[Pull and Fetch]] — Receive commits from remotes
