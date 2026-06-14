---
id: git-037-0000-0000-0000-000000000037
title: Git Bundles
language: markdown
tags: [git, bundles]
selection: null
isPinned: false
timestamp: 1781500000037
---
# Git Bundles

**Links**: [[Git Archive]] | [[Git Remote]] | [[Git Init and Clone]] | [[Git Workflows]]

## What are Bundles?

A Git bundle is a **single file** containing one or more Git refs and their objects. It's Git's equivalent of "export to file" — you can share commits without a server.

## When to Use Bundles

- **Air-gapped systems**: No network access to Git server
- **Transfer via USB/email**: Send commits as a file
- **Backup**: Save refs as a portable file
- **Offline sharing**: Give someone commits without a remote
- **Incremental updates**: Send only new commits since last bundle

## Creating a Bundle

```bash
# Bundle all branches
git bundle create repo.bundle --all

# Bundle specific branch
git bundle create feature.bundle feature

# Bundle since a specific commit
git bundle create updates.bundle main ^v1.0.0

# Bundle with tags
git bundle create release.bundle --tags

# Bundle a range of commits
git bundle create delta.bundle HEAD~10..HEAD
```

## Cloning from a Bundle

```bash
# Clone directly from bundle file
git clone repo.bundle new-repo
cd new-repo
git remote add origin https://github.com/user/repo.git
```

## Fetching from a Bundle

```bash
# Fetch updates from a bundle into existing repo
git fetch updates.bundle main:main

# Fetch all refs from bundle
git fetch feature.bundle '*:*'
```

## Verifying Bundles

```bash
# Check what's in a bundle
git bundle verify repo.bundle

# List refs in a bundle
git bundle list-heads repo.bundle

# Check if bundle is valid for a repo
git bundle verify updates.bundle
```

## Bundle Workflow Example

```bash
# On machine A: create incremental bundle
git bundle create update.bundle main ^last-bundle

# Transfer update.bundle via USB to machine B

# On machine B: apply the bundle
git fetch update.bundle main:main
```

## Bundle Commands Summary

| Command | Purpose |
|---------|---------|
| `git bundle create <file> <refs>` | Create a bundle |
| `git bundle verify <file>` | Check bundle validity |
| `git bundle list-heads <file>` | Show refs in bundle |
| `git bundle unbundle <file>` | Show objects in bundle |

**Next**: [[Git Aliases]] — Productivity shortcuts
