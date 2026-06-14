---
id: git-043-0000-0000-0000-000000000043
title: Git Internals II
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000043
---
# Git Internals II — Refs and Packfiles

**Links**: [[Git Internals I]] | [[Git Internals III]] | [[Git Branch]] | [[Git Reflog]] | [[Git Maintenance]]

## References (Refs)

Refs are pointers to commits stored as files in `.git/refs/`.

```
.git/refs/
├── heads/          # Local branches
│   ├── main        → a1b2c3d...
│   └── feature     → e4f5g6h...
├── tags/           # Tags
│   └── v1.0.0      → j4k5l6m...
└── remotes/        # Remote-tracking branches
    └── origin/
        ├── main    → f1g2h3i...
        └── feature → m7n8o9p...
```

You can read a ref directly:

```bash
cat .git/refs/heads/main
# a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b
```

## Packed Refs

For performance, Git packs refs into `.git/packed-refs`:

```
# pack-refs with: peeled fully-peeled sorted
a1b2c3d refs/heads/main
e4f5g6h refs/heads/feature
j4k5l6m refs/tags/v1.0.0
```

## HEAD

HEAD indicates the current branch or commit:

```bash
cat .git/HEAD
# ref: refs/heads/main       (on a branch)
# OR
# a1b2c3d4...                (detached HEAD)
```

## Packfiles

Git stores objects as **loose objects** initially. Over time, they get packed into `.pack` files for efficiency.

```
.git/objects/pack/
├── pack-a1b2c3d4.pack    # Compressed objects
└── pack-a1b2c3d4.idx     # Index for fast lookup
```

A packfile uses **delta compression** — storing just the differences between versions of the same file.

## Checking Packfiles

```bash
# Verify pack integrity
git verify-pack .git/objects/pack/*.pack
```

**Next**: [[Git Internals III]] — Maintenance and garbage collection