---
id: git-012-0000-0000-0000-000000000012
title: Git Conflict Resolution
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000012
---
# Git Conflict Resolution

**Links**: [[Git Merge]] | [[Git Rebase]] | [[Git Diff]] | [[Git Checkout and Switch]] | [[Git Advanced Merging]]

## When Conflicts Happen

Conflicts occur when two branches modify the **same lines** of the **same file** differently. Git cannot automatically decide which version to keep.

## Conflict Markers

When a conflict occurs, Git inserts markers into the file:

```
<<<<<<< HEAD
This is the version on the current branch
=======
This is the version from the incoming branch
>>>>>>> feature-branch
```

- `<<<<<<< HEAD` — Start of our version
- `=======` — Separator between versions
- `>>>>>>> feature-branch` — End of their version

## Resolving Conflicts

```bash
# 1. See which files have conflicts
git status

# 2. Open the conflicted files and edit them
#    Remove markers and keep the correct version

# 3. Mark as resolved
git add resolved-file.txt

# 4. Complete the merge
git commit
```

## Resolution Tools

```bash
# Use mergetool
git mergetool

# See full conflict diff
git diff

# See conflict summary
git log --merge
```

## Strategies

| Strategy | Command | Effect |
|----------|---------|--------|
| Keep ours | `git checkout --ours file.txt` | Accept current branch |
| Keep theirs | `git checkout --theirs file.txt` | Accept incoming branch |
| Manual edit | Edit file directly | Most control |

## Abort a Merge

```bash
# Go back to before merge started
git merge --abort
```

**Next**: [[Git Rebase]] — Reapply commits on top of another branch