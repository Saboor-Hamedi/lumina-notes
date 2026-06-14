---
id: git-001-0000-0000-0000-000000000001
title: Git Overview
language: markdown
tags: [git, vcs]
selection: null
isPinned: true
timestamp: 1781500000001
---
# Git Overview

**Links**: [[Git Installation]] | [[Git Configuration]] | [[Git Init and Clone]] | [[Git Add and Status]] | [[Git Commit]] | [[Git Branch]] | [[Git Remote]] | [[Git Workflows]] | [[Git Internals I]]

## What is Git?

Git is a **distributed version control system** (DVCS) created by Linus Torvalds in 2005 for Linux kernel development. Unlike centralized systems (SVN, CVS), every developer has a full copy of the entire repository history.

## Why Git?

| Feature | Benefit |
|---------|---------|
| Distributed | Work offline, full backup on every clone |
| Branching | Lightweight branches, cheap context switching |
| Integrity | SHA-1 hashing ensures data integrity |
| Speed | Most operations are local, no network needed |
| Staging Area | Fine-grained control over commits |

## Key Concepts

- **Repository (Repo)**: A Git database containing all files and history
- **Working Tree**: The directory where you edit files
- **Staging Area (Index)**: Intermediate area between working tree and repository
- **Commit**: A snapshot of the entire project at a point in time
- **Branch**: A movable pointer to a commit
- **HEAD**: Pointer to the current branch/commit

## The Three States

Every file in Git can be in one of three states:

```
Modified → Staged → Committed
   ↑           ↓        ↓
Working Tree → Index → Repository
```

- **Modified**: Changed but not yet staged
- **Staged**: Marked for inclusion in next commit
- **Committed**: Safely stored in the database

## Data Integrity

Git uses SHA-1 hashes (40-character hex strings) to identify every object. If a file changes, its hash changes. This makes it impossible to change history without detection.

**Next**: [[Git Installation]] — Install Git on your system