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

**Links**: [[Git Remote]] | [[Git Pull and Fetch]] | [[Git Branch]] | [[Git Rebase]] | [[Git Workflows]] | [[Git Reflog]]

## Basic Push

```bash
# Push to origin/main
git push origin main

# Push current branch to matching remote branch
git push

# Push and set upstream (-u sets tracking)
git push -u origin feature
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

**Next**: [[Git Pull and Fetch]] — Receive commits from remotes