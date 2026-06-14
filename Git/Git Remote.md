---
id: git-015-0000-0000-0000-000000000015
title: Git Remote
language: markdown
tags: [git, remote]
selection: null
isPinned: false
timestamp: 1781500000015
---
# Git Remote

**Links**: [[Git Overview]] | [[Git Init and Clone]] | [[Git Push]] | [[Git Pull and Fetch]] | [[Git Branch]] | [[Git Workflows]]

## What are Remotes?

Remotes are versions of your repository hosted on another server (GitHub, GitLab, self-hosted). They enable collaboration.

## Managing Remotes

```bash
# List remotes
git remote -v

# Add a remote
git remote add origin https://github.com/user/repo.git

# Remove a remote
git remote remove origin

# Rename a remote
git remote rename origin upstream

# Show remote info
git remote show origin
```

## Common Remote Names

| Name | Purpose |
|------|---------|
| `origin` | Your fork / main remote |
| `upstream` | Original repo you forked from |
| `backup` | Secondary backup remote |

## Protocols

| Protocol | URL Format | Auth |
|----------|------------|------|
| HTTPS | `https://github.com/user/repo.git` | Token/password |
| SSH | `git@github.com:user/repo.git` | SSH key |
| Local | `/path/to/repo.git` | Filesystem |

## Tracking Branches

```bash
# Push and set upstream
git push -u origin feature

# See tracking relationships
git branch -vv

# A tracking branch follows a remote branch
# main → origin/main
```

**Next**: [[Git Push]] — Send commits to remotes