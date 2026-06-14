---
id: git-034-0000-0000-0000-000000000034
title: Git Attributes
language: markdown
tags: [git, attributes]
selection: null
isPinned: false
timestamp: 1781500000034
---
# Git Attributes

**Links**: [[Git Configuration]] | [[Git Ignore]] | [[Git LFS]] | [[Git Diff]]

## What are .gitattributes?

`.gitattributes` is a file that sets attributes for specific file paths, controlling how Git handles them.

## File Location

You can have `.gitattributes` in:
1. The repo root (most common)
2. Any subdirectory (affects that subtree)
3. `.git/info/attributes` (local only, not committed)

## Common Attributes

### Line Endings

```gitattributes
# Auto-detect (recommended)
* text=auto

# Force LF (Unix/macOS)
*.sh text eol=lf

# Force CRLF (Windows scripts)
*.bat text eol=crlf

# Binary files (no conversion)
*.png binary
*.jpg binary
*.ico binary
```

### Diff Configuration

```gitattributes
# Use word-diff for markdown
*.md diff=markdown

# Binary files appear as "Binary files differ"
*.zip binary
```

### Merge Strategy

```gitattributes
# Always keep our version on conflict
*.lock merge=ours

# Use union merge (both sides)
*.cfg merge=union
```

### Language Detection (for GitHub)

```gitattributes
# Tell GitHub what language a file is
*.es6 linguist-language=JavaScript
*.h linguist-language=C
```

## Example .gitattributes

```gitattributes
# Auto detect text files
* text=auto

# Source code
*.py text diff=python
*.js text diff=javascript
*.ts text diff=typescript
*.sql text diff=sql
*.md text diff=markdown

# Binaries
*.png binary
*.jpg binary
*.ico binary
*.pdf binary

# Lock files
package-lock.json merge=ours
```

**Next**: [[Git Ignore]] — Ignore files with .gitignore