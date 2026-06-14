---
id: git-027-0000-0000-0000-000000000027
title: Git Reflog
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000027
---
# Git Reflog

**Links**: [[Git Reset]] | [[Git Revert]] | [[Git Reset]] | [[Git Bisect]] | [[Git Internals II]]

## What is Reflog?

The **reference log** (reflog) records every time HEAD moves — commits, checkouts, resets, rebases, merges. It's your safety net for recovering lost commits.

## Viewing Reflog

```bash
# Show reflog for HEAD
git reflog

# Show reflog for a specific branch
git reflog main

# Show with date
git reflog --date=iso

# Limit entries
git reflog -10
```

## Reflog Output

```
a1b2c3d HEAD@{0}: commit: Fix login validation
e4f5g6h HEAD@{1}: checkout: moving from main to feature
j4k5l6h HEAD@{2}: reset: moving to HEAD~1
m7n8o9p HEAD@{3}: commit: Add user profile page
```

`HEAD@{n}` is a relative reference — `HEAD@{0}` is current, `HEAD@{1}` is previous.

## Recovery Examples

```bash
# Oops: reset --hard too far
git reset --hard HEAD~5   # Lost 5 commits

# Find them in reflog
git reflog
# a1b2c3d HEAD@{1}: commit: Important work

# Recover
git reset --hard a1b2c3d
```

```bash
# Oops: deleted a branch
git branch -D feature

# Find last commit of feature
git reflog
git checkout -b feature a1b2c3d
```

## Time-Based Reflog

```bash
# What was HEAD yesterday?
git reflog --before="yesterday"

# Recover from 30 minutes ago
git checkout HEAD@{30.minutes.ago}
```

## Reflog Expiry

```bash
# Reflog entries expire (default 90 days)
git reflog expire --expire=90.days

# Keep reflog entries longer
git config --global gc.reflogExpire 120.days
```

**Next**: [[Git Submodules]] — Include other repositories