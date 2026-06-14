---
id: git-007-0000-0000-0000-000000000007
title: Git Log
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000007
---
# Git Log

**Links**: [[Git Commit]] | [[Git Diff]] | [[Git Branch]] | [[Git Reflog]] | [[Git Blame]] | [[Git Bisect]]

## Basic Usage

```bash
git log                              # Full history
git log --oneline                    # One line per commit
git log --oneline --graph --all      # Branch graph visualization
git log --since="2024-01-01"
git log --until="2024-06-01"
git log --author="Jane"
git log --grep="bugfix"              # Search commit messages
```

## Formatting Output

```bash
git log --pretty=format:"%h - %an, %ar : %s"
git log --format="%C(yellow)%h%Creset %s %Cgreen(%cr)%Creset"
```

Common placeholders: `%h` (hash), `%an` (author), `%ar` (relative date), `%s` (subject), `%d` (ref names).

## Filtering

```bash
git log -p                           # Show diffs inline
git log --stat                       # Show file statistics
git log --name-only                  # Show changed files
git log -S "function_name"           # Show commits that changed this string
git log --follow file.txt            # Show history of renamed file
git log branch1..branch2             # Commits in branch2 not in branch1
git log --merges                     # Only merge commits
git log --no-merges                  # Exclude merge commits
```

## Range and Path

```bash
git log main..feature                # Commits in feature not in main
git log --all -- src/                # Commits affecting src/ directory
git log --since="2 weeks ago" --until="1 week ago"
```

**Next**: [[Git Diff]] — View changes between commits