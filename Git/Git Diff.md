---
id: git-008-0000-0000-0000-000000000008
title: Git Diff
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000008
---
# Git Diff

**Links**: [[Git Add and Status]] | [[Git Commit]] | [[Git Log]] | [[Git Branch]] | [[Git Merge]] | [[Git Conflict Resolution]]

## Comparing Changes

```bash
# Working tree vs staging
git diff

# Staging vs last commit
git diff --cached        # or git diff --staged

# Working tree vs last commit
git diff HEAD

# Between two commits
git diff a1b2c3d..e4f5g6h

# Between branches
git diff main..feature

# Summary of changes
git diff --stat

# Only file names
git diff --name-only
```

## Options

```bash
# Word diff instead of line diff
git diff --word-diff

# Ignore whitespace
git diff --ignore-space-change
git diff --ignore-all-space

# Show function context
git diff --function-context

# Better algorithm
git diff --diff-algorithm=histogram
```

## External Diff Tools

```bash
# Configure beyond compare
git config --global diff.tool bc3
git config --global difftool.bc3.path "C:/Program Files/Beyond Compare/BCompare.exe"

# Use external tool
git difftool

# Directory diff
git difftool --dir-diff
```

**Next**: [[Git Branch]] — Work with branches