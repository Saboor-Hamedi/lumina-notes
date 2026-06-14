---
id: git-040-0000-0000-0000-000000000040
title: Git Pull Requests
language: markdown
tags: [git, pull-requests]
selection: null
isPinned: false
timestamp: 1781500000040
---
# Git Pull Requests

**Links**: [[Git Workflows]] | [[Git Branch]] | [[Git Remote]] | [[Git Push]] | [[Git Merge]] | [[Git Rebase]] | [[Git Hooks]]

## What is a Pull Request?

A pull request (PR) is a proposal to merge changes from one branch into another. It enables code review, discussion, and automated checks before merging.

## PR Workflow

```
1. Create branch   →  2. Make changes   →  3. Push   →  4. Open PR   →  5. Review   →  6. Merge
```

### Step 1: Create a Feature Branch

```bash
git checkout -b feature/user-auth
git push -u origin feature/user-auth
```

### Step 2: Make and Commit Changes

```bash
git add -A
git commit -m "Add user authentication module"
git push
```

### Step 3: Open a PR (via web or CLI)

```bash
# Using GitHub CLI
gh pr create --title "Add user authentication" --body "Implements login, register, and password reset"

# Using gh with template
gh pr create --fill
```

### Step 4: Code Review

Reviewers comment on the PR. Address feedback with new commits:

```bash
git add -A
git commit -m "Address review feedback: fix validation edge case"
git push
```

### Step 5: Merge

```bash
# Merge via web interface (recommended)
# Or via CLI
gh pr merge --squash
```

## PR Best Practices

| Practice | Why |
|----------|-----|
| Keep PRs small (< 400 lines) | Easier to review thoroughly |
| Write a clear description | Context helps reviewers |
| Reference issues | `Closes #123`, `Fixes JIRA-456` |
| Self-review first | Catch obvious mistakes |
| Respond to feedback | Explain your decisions |
| Rebase before merging | Keep history clean |

## PR Merge Strategies

| Strategy | History | When |
|----------|---------|------|
| Create merge commit | Preserves branch | Feature branches |
| Squash and merge | Single commit | Small features, fixes |
| Rebase and merge | Linear, no merge commits | Clean history |

**Next**: [[Git Advanced Merging]] — Complex merge scenarios