---
id: git-003-0000-0000-0000-000000000003
title: Git Configuration
language: markdown
tags: [git, configuration]
selection: null
isPinned: false
timestamp: 1781500000003
---
# Git Configuration

**Links**: [[Git Overview]] | [[Git Installation]] | [[Git Init and Clone]] | [[Git Aliases]] | [[Git Attributes]] | [[Git Hooks]] | [[Git Best Practices]]

## Configuration Levels

Git has three levels, each overriding the previous:

| Level | Scope | File | Flag |
|-------|-------|------|------|
| System | All users | `/etc/gitconfig` | `--system` |
| Global | Your account | `~/.gitconfig` | `--global` |
| Local | Current repo | `.git/config` | `--local` (default) |

```bash
# View all settings with origin
git config --list --show-origin

# View specific level
git config --global --list
git config --local --list
```

## Essential Settings

```bash
git config --global user.name "Jane Doe"
git config --global user.email "jane@example.com"
git config --global init.defaultBranch main
git config --global core.editor "code --wait"
git config --global color.ui auto
```

## Platform-Specific

```bash
# macOS/Linux: LF line endings
git config --global core.autocrlf input

# Windows: CRLF -> LF on commit, LF -> CRLF on checkout
git config --global core.autocrlf true
```

## Useful Settings

```bash
git config --global pull.rebase true
git config --global rebase.autoStash true
git config --global merge.conflictStyle zdiff3
git config --global diff.algorithm histogram
git config --global push.default current
git config --global fetch.prune true
```

## Viewing and Editing

```bash
# Open config in editor
git config --global --edit

# Get a single value
git config user.name

# Unset a value
git config --global --unset user.name

# Conditional includes (different config per path)
git config --global includeIf.gitdir:~/work/.path "~/work/.gitconfig"
```

## Common Configurations for Teams

```bash
# Safe force push
git config --global pull.rebase true

# Better merges
git config --global merge.ff false

# Case-insensitive filenames (Windows/macOS)
git config --global core.ignorecase true
```

**Next**: [[Git Init and Clone]] — Create or copy repositories
