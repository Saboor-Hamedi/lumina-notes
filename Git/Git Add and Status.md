---
id: git-005-0000-0000-0000-000000000005
title: Git Add and Status
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000005
---
# Git Add and Status

**Links**: [[Git Overview]] | [[Git Init and Clone]] | [[Git Commit]] | [[Git Diff]] | [[Git Restore]] | [[Git Clean]] | [[Git Ignore]]

## The Staging Area

Git's staging area (index) sits between your working tree and the repository:

```
Working Tree → git add → Staging Area → git commit → Repository
```

## git status

Shows the state of your working tree and staging area.

```bash
git status        # Verbose
git status -s     # Short: M modified, ?? untracked
```

## git add

Moves changes from working tree to staging area.

```bash
git add file.txt        # Single file
git add .               # Current directory
git add -A              # All changes everywhere
git add -u              # Only tracked files
git add -p              # Interactive (hunk by hunk)
git add -f file.txt     # Force add (bypass .gitignore)
```

## Interactive Add (-p)

With `-p`, Git shows each change hunk and asks:

| Option | Meaning |
|--------|---------|
| y | Stage this hunk |
| n | Don't stage |
| s | Split into smaller hunks |
| e | Edit hunk manually |
| q | Quit |

**Next**: [[Git Commit]] — Create snapshots of your changes