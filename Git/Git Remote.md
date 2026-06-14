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

# Change remote URL
git remote set-url origin https://github.com/user/new-repo.git
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

## Changing Remote URL

```bash
# Switch from HTTPS to SSH
git remote set-url origin git@github.com:user/repo.git

# Add a second URL (push to multiple remotes)
git remote set-url --add --push origin git@github.com:user/repo.git
git remote set-url --add --push origin git@gitlab.com:user/repo.git
```

## Tracking Branches

```bash
# Push and set upstream
git push -u origin feature

# See tracking relationships
git branch -vv

# Set upstream for existing branch
git branch -u origin/feature feature

# A tracking branch follows a remote branch
# main → origin/main
```

## Remote Operations Table

| Command | Purpose |
|---------|---------|
| `git remote -v` | List remotes with URLs |
| `git remote add <name> <url>` | Add a remote |
| `git remote rename <old> <new>` | Rename a remote |
| `git remote remove <name>` | Delete a remote |
| `git remote show <name>` | Verbose remote info |
| `git remote update` | Fetch all remotes |
| `git remote prune origin` | Prune stale tracking branches |

**Next**: [[Git Push]] — Send commits to remotes
