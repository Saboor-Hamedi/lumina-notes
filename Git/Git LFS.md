---
id: git-032-0000-0000-0000-000000000032
title: Git LFS
language: markdown
tags: [git, lfs]
selection: null
isPinned: false
timestamp: 1781500000032
---
# Git LFS

**Links**: [[Git Submodules]] | [[Git Best Practices]] | [[Git Attributes]]

## What is Git LFS?

Git LFS (Large File Storage) replaces large files (binaries, assets, datasets) with text pointers in your repo, storing the actual file content on a remote server.

## Why LFS?

| Problem | Solution |
|---------|----------|
| Git repos grow huge with binaries | LFS stores content externally |
| Clones are slow | Only pointers are cloned |
| Diffing binaries is useless | LFS can diff pointers |

## Common Use Cases

- Images, videos, audio files
- Compiled binaries and DLLs
- Game assets (models, textures)
- ML model files and datasets
- Design files (PSD, AI, Figma exports)

## Installation

```bash
# Install LFS
git lfs install

# Verify
git lfs version
```

## Using LFS

```bash
# Track a file type
git lfs track "*.psd"
git lfs track "*.mp4"

# Track a specific file
git lfs track "assets/logo.png"

# The patterns go in .gitattributes
cat .gitattributes
# *.psd filter=lfs diff=lfs merge=lfs -text
```

## Working with LFS

```bash
# Add and commit as normal
git add assets/logo.psd
git commit -m "Add logo design files"
git push                            # LFS files are uploaded separately
```

## Pulling LFS Files

```bash
# Normal clone includes LFS pointers
git clone https://github.com/user/repo.git

# Fetch actual LFS content
git lfs pull

# Clone with LFS content
GIT_LFS_SKIP_SMUDGE=0 git clone https://github.com/user/repo.git
```

## LFS Commands

```bash
# List tracked patterns
git lfs track

# List tracked files
git lfs ls-files

# Migrate existing files to LFS
git lfs migrate import --include="*.zip"

# Check LFS usage
git lfs status
```

**Next**: [[Git GPG]] — Signing commits and tags