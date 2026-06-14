---
id: git-041-0000-0000-0000-000000000041
title: Git Advanced Merging
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000041
---
# Git Advanced Merging

**Links**: [[Git Merge]] | [[Git Conflict Resolution]] | [[Git Rebase]] | [[Git Cherry-Pick]] | [[Git Workflows]]

## Merge Strategies

### Recursive (Default)

Three-way merge using a common ancestor. Handles most cases.

```bash
git merge feature

# Patience mode (better for large reorganizations)
git merge --strategy-opt patience feature
```

### Ours

Keeps the current branch version entirely — discards all incoming changes.

```bash
git merge -s ours feature
# Result: merge commit exists, but all changes are from current branch
```

### Subtree

Used when merging a subproject into a subdirectory.

```bash
git merge -s subtree feature
```

## Handling Special Cases

### Merge with Renamed Files

Git detects renames using similarity. If conflicts occur:

```bash
# Increase rename threshold
git merge --find-renames=50% feature

# Or disable rename detection
git merge --no-renames feature
```

### Merge with Deleted Files

When one branch deletes a file and another modifies it:

```
CONFLICT (modify/delete): file.txt deleted in feature and modified in HEAD
```

Resolution:
```bash
git checkout --ours file.txt   # Keep file
git rm file.txt                # Accept deletion
```

### Merge with Unrelated Histories

```bash
# Allow merging unrelated projects
git merge --allow-unrelated-histories other-repo/main
```

## Ver Strategy

Skip strategy entirely (just check if fast-forward possible):

```bash
git merge --verify-signatures feature
# Rejects if commits aren't signed
```

## Automating Merge

```bash
# If merge has no conflicts, open editor for commit message
git merge --no-edit feature    # Use default message

# Automatically resolve simple conflicts
git merge -X ours feature      # Auto-resolve with ours
git merge -X theirs feature    # Auto-resolve with theirs
```

**Next**: [[Git Internals I]] — Objects (blob, tree, commit)