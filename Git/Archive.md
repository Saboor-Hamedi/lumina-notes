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

**Links**: [[Init and Clone]] | [[Submodules]] | [[Tag]] | [[Workflows]]

Sometimes you need a repository snapshot without the .git directory. The archive command creates compressed tarballs or zip files of your project at any point in history.

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

## Supported Formats

| Format | Flag | Extension |
|--------|------|-----------|
| tar | `--format=tar` | `.tar` |
| zip | `--format=zip` | `.zip` |
| tar.gz | `--format=tar.gz` | `.tar.gz` |
| Auto | Omit flag | Detected from output filename |

## Prefix

Add a prefix to all files in the archive:

```bash
git archive --output=project.tar --prefix=myapp/ HEAD
# All files inside: myapp/src/, myapp/README.md, etc.

# Useful for extraction with prefix
tar -xf project.tar    # All files under myapp/
```

## Archive Specific Path

```bash
# Archive only a subdirectory
git archive --output=src.zip HEAD:src/

# Archive specific files
git archive --output=docs.tar HEAD:docs/ HEAD:README.md
```

## Common Uses

| Scenario | Command |
|----------|---------|
| Deliver source to client | `git archive --format=zip v1.0 > release.zip` |
| Deploy to server | `git archive HEAD \| tar -x -C /deploy/path` |
| Share without .git | `git archive --format=tar HEAD \| gzip > project.tar.gz` |
| Package a subdirectory | `git archive --output=src.zip HEAD:src/` |
| Archive with version prefix | `git archive --prefix=app-v2.1/ --output=app-v2.1.tar v2.1` |

## Pipe to Remote

```bash
# Extract directly on remote server
git archive HEAD | ssh user@server "tar -x -C /var/www/app"

# Pipe through gzip for large archives
git archive --format=tar HEAD | gzip | ssh user@server "tar -xz -C /var/www/app"
```

**Next**: [[Bundles]] — Share via file
