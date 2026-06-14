---
id: git-024-0000-0000-0000-000000000024
title: Git Blame
language: markdown
tags: [git, blame]
selection: null
isPinned: false
timestamp: 1781500000024
---
# Git Blame

**Links**: [[Git Log]] | [[Git Diff]] | [[Git Commit]] | [[Git Bisect]]

## What is Blame?

`git blame` shows line-by-line annotation of a file, revealing who last modified each line and in which commit.

## Basic Usage

```bash
# Annotate entire file
git blame file.txt

# Specific line range (lines 10-30)
git blame -L 10,30 file.txt

# Show email instead of name
git blame -e file.txt
```

## Output Format

```
a1b2c3d4 (Jane Doe 2024-01-15 14:30:00 +0000 42) function processData() {
^---hash    ^---author   ^---date            ^---line  ^---content
```

- `^` prefix = line hasn't changed since initial commit
- Movement detection shows if lines were moved within file

## Advanced Options

```bash
# Detect moved lines within the file
git blame -M file.txt

# Detect moved lines across files
git blame -C file.txt

# Detect moved lines across files + cross-commit
git blame -CCC file.txt

# Show raw timestamp
git blame --date=short file.txt

# Ignore whitespace changes
git blame -w file.txt
```

## Practical Uses

| Scenario | Command |
|----------|---------|
| Find who introduced a bug | `git blame -L 50,60 buggy.py` |
| When was this function added? | `git blame -L '/def process/','/^def/' app.py` |
| Ignore formatting commits | `git blame --ignore-revs-file .git-blame-ignore-revs` |

**Next**: [[Git Bisect]] — Binary search for bugs