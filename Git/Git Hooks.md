---
id: git-030-0000-0000-0000-000000000030
title: Git Hooks
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500000030
---
# Git Hooks

**Links**: [[Git Configuration]] | [[Git Server Hooks]] | [[Git Best Practices]] | [[Developer Workflow Automation]]

## What are Hooks?

Git hooks are scripts that run automatically on specific Git events. They live in `.git/hooks/` and are **not committed** to the repository.

## Client-Side Hooks

### Pre-Commit Hook

Runs before each commit. Used for linting, formatting, and testing.

```bash
# .git/hooks/pre-commit (must be executable)
#!/bin/sh
ruff check .
if [ $? -ne 0 ]; then
    echo "Linting failed!"
    exit 1
fi
```

### Prepare-Commit-Message

Edits the default commit message. Used to add issue numbers.

```bash
# Add branch name to commit message
#!/bin/sh
BRANCH=$(git rev-parse --abbrev-ref HEAD)
echo "[$BRANCH] $(cat $1)" > $1
```

### Post-Commit Hook

Runs after commit. Used for notifications.

```bash
#!/bin/sh
COMMIT_MSG=$(git log -1 --pretty=%B)
curl -X POST https://slack.com/api/chat.postMessage \
  -d "text=New commit: $COMMIT_MSG"
```

### Pre-Push Hook

Runs before push. Used for integration tests.

```bash
#!/bin/sh
echo "Running tests before push..."
pytest
if [ $? -ne 0 ]; then
    echo "Tests failed, push rejected"
    exit 1
fi
```

## Sample Hook Templates

Git ships example hooks in `.git/hooks/` with `.sample` extension:

```bash
# Enable a sample hook
mv .git/hooks/pre-commit.sample .git/hooks/pre-commit
```

## Sharing Hooks

Since hooks aren't committed, use one of these:

| Method | How |
|--------|-----|
| Script in repo | Store `scripts/pre-commit`, symlink to `.git/hooks/` |
| Hook manager | Use **husky** (JS), **pre-commit** (Python) |
| Template directory | `git config --global init.templateDir ~/.git-templates` |

**Next**: [[Git Server Hooks]] — Server-side enforcement