---
id: git-044-0000-0000-0000-000000000044
title: Git Internals III
language: markdown
tags: [git, internals]
selection: null
isPinned: false
timestamp: 1781500000044
---
# Git Internals III — Maintenance

**Links**: [[Git Internals I]] | [[Git Internals II]] | [[Git Reflog]] | [[Git Best Practices]]

## Garbage Collection

Git periodically compresses loose objects into packfiles and removes unreachable objects.

```bash
# Run garbage collection manually
git gc

# Aggressive (slower but better compression)
git gc --aggressive

# With pruning (remove unreachable objects)
git gc --prune=now
```

## When GC Runs Automatically

- After `git rebase`, `git merge`, `git commit` (when loose objects exceed limits)
- Typically ~7000 loose objects triggers auto-gc
- Configurable: `git config --global gc.auto 5000`

## git repack

Forces repacking of all objects:

```bash
# Repack all objects
git repack -a -d

# With delta compression
git repack -a -d --window=250 --depth=50
```

## git prune

Removes unreachable (dangling) objects:

```bash
# Dry run (show what would be removed)
git prune --dry-run

# Remove objects older than 2 weeks
git prune --expire=2.weeks

# Remove all unreachable objects
git prune --expire=now
```

## git fsck

Filesystem check — verifies repository integrity:

```bash
# Full integrity check
git fsck

# Show dangling objects
git fsck --dangling

# Show all objects
git fsck --full
```

## Repository Size

```bash
# Show total object count
git count-objects -v

# Find largest files
git rev-list --objects --all | git cat-file --batch-check='%(objectsize:disk) %(rest)' | sort -rn | head -10
```

**Next**: [[Git Best Practices]] — Tips and conventions