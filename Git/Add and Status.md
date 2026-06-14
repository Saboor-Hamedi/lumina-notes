---
id: git-005-0000-0000-0000-000000000005
title: Git Add and Status
language: markdown
tags: [git, staging]
selection: null
isPinned: false
timestamp: 1781500000005
---
# Git Add and Status

**Links**: [[Overview]] | [[Init and Clone]] | [[Commit]] | [[Diff]] | [[Restore]] | [[Clean]] | [[Ignore]]

Before committing, Git lets you selectively stage changes. The staging area (index) is an intermediate space — a preview of your next commit that gives you fine-grained control over what gets recorded.

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
git status -b     # Show branch info
git status --ignored  # Show ignored files too
```

## Status Symbols (Short Format)

| Symbol | Meaning |
|--------|---------|
| `M` | Modified |
| `A` | Added |
| `D` | Deleted |
| `R` | Renamed |
| `??` | Untracked |
| `!!` | Ignored |
| `MM` | Staged and modified again |

## git add

Moves changes from working tree to staging area.

```bash
git add file.txt        # Single file
git add .               # Current directory
git add -A              # All changes everywhere
git add -u              # Only tracked files
git add -p              # Interactive (hunk by hunk)
git add -f file.txt     # Force add (bypass .gitignore)
git add --dry-run .     # Preview what would be added
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
| a | Stage this and remaining hunks |
| d | Don't stage this or remaining |
| j | Leave this undecided, see next |

## Practical Workflow

```bash
# Quick check
git status -s

# Stage specific files for a focused commit
git add src/login.ts
git add src/utils/validation.ts

# Review what's staged
git diff --cached

# Commit
git commit -m "Add login validation"
```

```mermaid
flowchart LR
    WT[Working Tree] -->|git add| ST[Staging Area]
    ST -->|git commit| RE[Repository]
    WT -->|git diff| ST
    ST -->|git diff --cached| RE
```

**Next**: [[Commit]] — Create snapshots of your changes
