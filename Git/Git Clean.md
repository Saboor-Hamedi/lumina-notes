---
id: git-023-0000-0000-0000-000000000023
title: Git Clean
language: markdown
tags: [git, clean]
selection: null
isPinned: false
timestamp: 1781500000023
---
# Git Clean

**Links**: [[Git Add and Status]] | [[Git Restore]] | [[Git Reset]] | [[Git Ignore]] | [[Git Stash]]

## What is Clean?

`git clean` removes untracked files from your working tree. Use it to get a pristine working directory.

## Dry Run First

Always preview what clean will delete:

```bash
# Preview what will be removed
git clean -n
git clean --dry-run

# Preview including directories
git clean -nd
```

## Basic Clean

```bash
# Remove untracked files
git clean -f

# Remove untracked files AND directories
git clean -fd

# Remove untracked files including ignored ones
git clean -fx

# Remove untracked + ignored files + directories
git clean -fdx
```

## Interactive Clean

```bash
git clean -i
# Would remove the following items:
# src/temp.log  build/
# *** Commands ***
# 1: clean     2: filter by pattern  3: select by numbers
```

## Interactive Commands

| Command | Meaning |
|---------|---------|
| 1: clean | Remove selected items |
| 2: filter by pattern | Filter files by glob |
| 3: select by numbers | Pick specific files |
| 4: ask each | Confirm each file |
| 5: quit | Exit without cleaning |
| 6: help | Show help |

## Safety Tips

| Flag | Behavior | Safe? |
|------|----------|-------|
| `-n` | Dry run (preview) | ✅ Safe |
| `-f` | Force clean | ⚠️ Destructive |
| `-d` | Include directories | ⚠️ Destructive |
| `-x` | Include .gitignore'd files | ❌ Very destructive |

**Always run `git clean -n` first** to see what will be deleted.

```bash
# Safe clean workflow
git clean -nfd          # Preview
# Verify the list
git clean -fd           # Execute
```

## Practical Scenarios

| Scenario | Command |
|----------|---------|
| Remove build artifacts | `git clean -fd` |
| Wipe everything including ignored | `git clean -fdx` |
| Remove only .log files | `git clean -f '*.log'` |
| Interactive selection | `git clean -i` |
| Clean entire repo to pristine state | `git clean -fdx && git checkout .` |

**Next**: [[Git Blame]] — Find who changed what
