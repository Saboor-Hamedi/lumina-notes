---
id: git-018-0000-0000-0000-000000000018
title: Git Tag
language: markdown
tags: [git, tagging]
selection: null
isPinned: false
timestamp: 1781500000018
---
# Git Tag

**Links**: [[Git Commit]] | [[Git Push]] | [[Git Branch]] | [[Git GPG]] | [[Git Workflows]]

## What are Tags?

Tags are **fixed pointers** to specific commits, typically used for releases (v1.0, v2.3.1). Unlike branches, tags don't move.

## Lightweight vs Annotated

| Type | Stores | Use |
|------|--------|-----|
| Lightweight | Just the hash | Temporary markers |
| Annotated | Hash + message + author + date | Releases, permanent markers |

## Creating Tags

```bash
# Lightweight tag
git tag v1.0.0
git tag v1.0.0 a1b2c3d      # Tag a specific commit

# Annotated tag (-a with -m)
git tag -a v1.0.0 -m "Release version 1.0.0"
git tag -a v1.0.0 -m "Release v1.0.0" a1b2c3d

# Show tag details
git show v1.0.0
```

## Managing Tags

```bash
# List tags
git tag
git tag --list "v2.*"       # Filter by pattern

# Delete a local tag
git tag -d v1.0.0

# Delete a remote tag
git push origin --delete v1.0.0

# Rename (delete + recreate)
git tag -d old-name
git tag -a new-name -m "Renamed"
```

## Sharing Tags

```bash
# Push a single tag
git push origin v1.0.0

# Push all tags
git push origin --tags

# Checkout a tag (detached HEAD)
git checkout v1.0.0

# Create branch from tag
git switch -c release-v1 v1.0.0
```

## Semantic Versioning

```
vMAJOR.MINOR.PATCH
v2.1.3
```

- **MAJOR**: Breaking changes
- **MINOR**: New features (backward compatible)
- **PATCH**: Bug fixes (backward compatible)

**Next**: [[Git Stash]] — Save and restore work in progress