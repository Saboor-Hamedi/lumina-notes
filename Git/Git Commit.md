---
id: git-006-0000-0000-0000-000000000006
title: Git Commit
language: markdown
tags: [git, commits]
selection: null
isPinned: false
timestamp: 1781500000006
---
# Git Commit

**Links**: [[Git Add and Status]] | [[Git Diff]] | [[Git Log]] | [[Git Reset]] | [[Git Revert]] | [[Git Rebase]] | [[Git Branch]] | [[Git Tag]] | [[Git GPG]]

## What is a Commit?

A commit is a **snapshot** of your entire project. Each commit has:
- Unique SHA-1 hash (e.g., `a1b2c3d...`)
- Author name, email, and timestamp
- Parent commit pointer(s)
- A commit message
- A tree pointer to all files

## Committing

```bash
git commit -m "Fix login form validation"
git commit -a -m "Stage tracked + commit"    # Skip staging for tracked files

# Write message in editor
git commit
```

## Good Commit Messages

```
Capitalized summary (50 chars max)
<blank line>
Detailed explanation wrapped at 72 chars.
Explain what and why, not how.

- Use imperative mood: "Add" not "Added"
- Reference issues: #123, JIRA-456
```

## Advanced

```bash
git commit --amend -m "Better message"          # Fix last commit
git commit --amend --no-edit                    # Add forgotten file
git commit --allow-empty -m "Trigger CI build"  # Empty commit
```

**Next**: [[Git Log]] — Explore commit history