---
id: git-004-0000-0000-0000-000000000004
title: Git Init and Clone
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000004
---
# Git Init and Clone

**Links**: [[Git Overview]] | [[Git Configuration]] | [[Git Add and Status]] | [[Git Commit]] | [[Git Remote]] | [[Git Branch]] | [[Git Worktrees]]

## git init

Creates a new Git repository.

```bash
git init                          # Current directory
git init my-project               # Specific directory
git init --bare my-project.git    # Bare repo (server, no working tree)
```

After `git init`, Git creates a `.git` directory:
```
.git/
├── HEAD
├── config
├── description
├── hooks/
├── info/
├── objects/
└── refs/
```

## git clone

Copies an existing repository.

```bash
git clone https://github.com/user/repo.git
git clone git@github.com:user/repo.git
git clone --branch develop https://github.com/user/repo.git
git clone --depth 1 https://github.com/user/repo.git    # Shallow
git clone --recursive https://github.com/user/repo.git   # With submodules
```

## Bare vs Non-Bare

| Type | Working Tree | Use |
|------|-------------|-----|
| Non-bare | Yes | Development |
| Bare | No | Server/hub repos |

**Next**: [[Git Add and Status]] — Track changes in your working tree