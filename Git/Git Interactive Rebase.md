---
id: git-014-0000-0000-0000-000000000014
title: Git Interactive Rebase
language: markdown
tags: [git, rebase]
selection: null
isPinned: false
timestamp: 1781500000014
---
# Git Interactive Rebase

**Links**: [[Git Rebase]] | [[Git Commit]] | [[Git Reset]] | [[Git Revert]] | [[Git Cherry-Pick]] | [[Git Workflows]]

## What is Interactive Rebase?

Interactive rebase (`rebase -i`) lets you edit, reorder, squash, split, or drop commits before applying them.

```bash
# Rebase last 3 commits
git rebase -i HEAD~3

# Rebase everything since branching from main
git rebase -i main
```

## The Interactive Editor

This opens an editor with a list of commits:

```
pick a1b2c3d Fix login form
pick e4f5g6h Add validation
pick j4k5l6m Update styles

# Commands:
# p, pick = use commit
# r, reword = use commit but edit message
# e, edit = use commit but stop for amending
# s, squash = combine with previous commit
# f, fixup = squash but discard message
# d, drop = remove commit
# x, exec = run command
```

## Common Operations

### Squash (Combine commits)

```
pick a1b2c3d Initial implementation
squash e4f5g6h Fix typo          ← merged into previous
squash j4k5l6h Add tests         ← merged into previous
```

### Reword (Edit message)

```
pick a1b2c3d Initial commit
reword e4f5g6h Bad message       ← prompts for new message
```

### Drop (Delete commit)

```
pick a1b2c3d Good commit
drop e4f5g6h Oops commit         ← removed from history
```

### Edit (Split a commit)

```
pick a1b2c3d Good
edit e4f5g6h To split            ← Git stops, you can unstage/reset
pick j4k5l6h Good
```

After `edit`, Git stops before that commit:
```bash
git reset HEAD^              # Unstage everything
git add -p                   # Stage first part
git commit -m "Part 1"
git add -A                   # Stage remaining
git commit -m "Part 2"
git rebase --continue
```

**Next**: [[Git Remote]] — Work with remote repositories