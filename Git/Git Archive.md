---
id: git-036-0000-0000-0000-000000000036
title: Git Archive
language: markdown
tags: [git, archive]
selection: null
isPinned: false
timestamp: 1781500000036
---
# Git Archive

**Links**: [[Git Init and Clone]] | [[Git Submodules]] | [[Git Tag]] | [[Git Workflows]]

## What is Archive?

`git archive` creates a compressed archive (tar or zip) of your repository at a specific commit, without the `.git` directory.

## Basic Usage

```bash
# Create tar archive of HEAD
git archive --output=project.tar HEAD

# Create zip archive
git archive --format=zip --output=project.zip HEAD

# Archive a specific tag
git archive --format=zip --output=project-v1.0.zip v1.0.0

# Archive a specific branch
git archive --format=tar --output=main.tar main
```

## Prefix

Add a prefix to all files in the archive:

```bash
git archive --output=project.tar --prefix=myapp/ HEAD
# All files inside: myapp/src/, myapp/README.md, etc.
```

## Archive Specific Path

```bash
# Archive only a subdirectory
git archive --output=src.zip HEAD:src/
```

## Common Uses

| Scenario | Command |
|----------|---------|
| Deliver source to client | `git archive --format=zip v1.0 > release.zip` |
| Deploy to server | `git archive HEAD | tar -x -C /deploy/path` |
| Share without .git | `git archive --format=tar HEAD \| gzip > project.tar.gz` |

## Pipe to Remote

```bash
# Extract directly on remote server
git archive HEAD | ssh user@server "tar -x -C /var/www/app"
```

**Next**: [[Git Bundles]] — Share via file