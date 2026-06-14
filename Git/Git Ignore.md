---
id: git-035-0000-0000-0000-000000000035
title: Git Ignore
language: markdown
tags: [git, gitignore]
selection: null
isPinned: false
timestamp: 1781500000035
---
# Git Ignore

**Links**: [[Git Add and Status]] | [[Git Clean]] | [[Git Attributes]] | [[Git LFS]] | [[Git Best Practices]]

## What is .gitignore?

`.gitignore` tells Git which files to ignore — they won't be tracked, staged, or committed.

## Basic Syntax

```gitignore
# Comments start with #
node_modules/       # Ignore entire directory
*.log               # Ignore all .log files
!important.log      # BUT track this one (negation)
build/              # Ignore build directory
/env                # Ignore file at specific path
*.tmp               # Ignore all .tmp files
```

## Common Patterns

```gitignore
# Dependencies
node_modules/
vendor/
.packages/

# Build output
dist/
build/
out/
*.exe
*.dll

# Environment
.env
.env.local
*.env

# IDE and editor
.idea/
.vscode/
*.swp
*.swo
.DS_Store
Thumbs.db

# Logs and temp
*.log
*.tmp
*.cache

# Python
__pycache__/
*.pyc
*.pyo
.venv/
venv/

# Node
node_modules/
npm-debug.log*
```

## Global .gitignore

For files you never want to track in any repo:

```bash
# Create a global gitignore
git config --global core.excludesFile ~/.gitignore_global

# Populate it
echo ".DS_Store" >> ~/.gitignore_global
echo "Thumbs.db" >> ~/.gitignore_global
```

## Check What's Ignored

```bash
# Check if a file is ignored
git check-ignore file.txt

# List all ignored files
git status --ignored
```

## Ignoring Files Already Tracked

If a file is already tracked, `.gitignore` won't stop it. Remove it first:

```bash
# Remove from tracking without deleting file
git rm --cached file.txt
```

**Next**: [[Git Archive]] — Export repositories