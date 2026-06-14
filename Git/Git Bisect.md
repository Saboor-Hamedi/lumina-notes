---
id: git-025-0000-0000-0000-000000000025
title: Git Bisect
language: markdown
tags: [git, bisect, debugging]
selection: null
isPinned: false
timestamp: 1781500000025
---
# Git Bisect

**Links**: [[Git Blame]] | [[Git Log]] | [[Git Reflog]] | [[Git Best Practices]]

## What is Bisect?

`git bisect` uses binary search to find the exact commit that introduced a bug. It's the most efficient way to find when something broke.

## How It Works

Git checks out commits between "good" and "bad", asking you to test each one. With binary search, it finds the culprit in **log₂(n)** steps.

## Manual Bisect

```bash
# Start bisect
git bisect start

# Mark current commit as bad
git bisect bad

# Mark a known-good commit
git bisect good v1.0.0

# Git checks out a midpoint commit
# Test it, then mark:
git bisect good    # If the bug isn't here
git bisect bad     # If the bug is here

# Repeat until culprit is found
# Git shows: a1b2c3d is the first bad commit
```

## Automated Bisect

```bash
# With a test script (exit 0 = good, exit 1 = bad)
git bisect run pytest test_login.py

# With any command
git bisect start
git bisect bad
git bisect good v1.0.0
git bisect run node test.js
```

## Reset

```bash
git bisect reset
```

## Tips

- Start bisect as soon as you notice a regression
- Good commit should be as close to bad as possible
- Use automated tests when available
- You can also bisect with `--term-old` and `--term-new` for custom terms

**Next**: [[Git Cherry-Pick]] — Apply specific commits