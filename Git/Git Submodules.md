---
id: git-028-0000-0000-0000-000000000028
title: Git Submodules
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000028
---
# Git Submodules

**Links**: [[Git Init and Clone]] | [[Git Remote]] | [[Git Worktrees]] | [[Git Workflows]]

## What are Submodules?

Submodules embed one Git repository inside another while keeping their histories separate. Useful for shared libraries, themes, or dependencies.

## Adding a Submodule

```bash
# Add a submodule
git submodule add https://github.com/user/library.git lib/

# Add at a specific branch
git submodule add -b stable https://github.com/user/library.git lib/
```

This creates:
- A `.gitmodules` file tracking submodule URLs
- A gitlink entry in the parent repo
- The submodule checked out

## Cloning with Submodules

```bash
# Clone and initialize submodules
git clone --recursive https://github.com/user/project.git

# Or after cloning
git clone https://github.com/user/project.git
git submodule init
git submodule update
```

## Updating Submodules

```bash
# Pull latest for all submodules
git submodule update --remote

# Pull latest for a specific submodule
git submodule update --remote lib/

# With merge strategy
git submodule update --remote --merge
git submodule update --remote --rebase
```

## Working with Submodules

```bash
# Enter submodule and work normally
cd lib/
git checkout main
git pull
cd ..

# Commit the new submodule pointer
git commit -am "Update lib to latest"
```

## Common Commands

```bash
# Show submodule status
git submodule status

# Check for changes in submodules
git submodule foreach git status

# Run command in every submodule
git submodule foreach git pull origin main

# Remove a submodule
git submodule deinit lib/
git rm lib/
rm -rf .git/modules/lib/
```

**Next**: [[Git Worktrees]] — Multiple working trees