---
id: git-042-0000-0000-0000-000000000042
title: Git Internals I
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000042
---
# Git Internals I — Objects

**Links**: [[Git Overview]] | [[Git Commit]] | [[Git Internals II]] | [[Git Internals III]] | [[Git Reflog]]

## Git's Object Model

Git is fundamentally a **content-addressable filesystem**. Everything is stored as an object identified by its SHA-1 hash.

```
Hash:  a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b
       ^--- 40 hex characters = 20 bytes = 160 bits
```

## Four Object Types

### 1. Blob (Binary Large Object)

Stores file content. Blobs have no name, just content.

```bash
# Create a blob
echo "Hello Git" | git hash-object --stdin -w
# Returns hash: 8baef1b4abc478178b...

# View a blob
git cat-file -p 8baef1b4
# Hello Git
```

### 2. Tree

Stores directory listings — filenames, modes, and pointers to blobs/trees.

```
tree a1b2c3d
├── README.md  blob e4f5g6h
├── src/
│   tree j4k5l6m
│   ├── main.py  blob m7n8o9p
│   └── utils.py  blob q1r2s3t
```

### 3. Commit

Points to a tree and parent commit(s), with metadata.

```
commit a1b2c3d
tree  e4f5g6h       → Project snapshot
parent j4k5l6m      → Previous commit
author Jane Doe
committer Jane Doe
date   2024-01-15

Add login form validation
```

### 4. Tag (annotated)

Points to a commit with additional metadata (tagger, message, signature).

## Inspecting Objects

```bash
# View any object
git cat-file -p <hash>

# Show type
git cat-file -t <hash>

# Show size
git cat-file -s <hash>
```

**Next**: [[Git Internals II]] — Refs and packfiles