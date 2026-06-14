---
id: git-012-0000-0000-0000-000000000012
title: Git Conflict Resolution
language: markdown
tags: [git, conflicts]
selection: null
isPinned: false
timestamp: 1781500000012
---
# Git Conflict Resolution

**Links**: [[Merge]] | [[Rebase]] | [[Diff]] | [[Checkout and Switch]] | [[Advanced Merging]]

Merge conflicts are inevitable when parallel work touches the same code. Git marks conflict regions and provides tools to resolve them systematically.

## When Conflicts Happen

Conflicts occur when two branches modify the **same lines** of the **same file** differently. Git cannot automatically decide which version to keep.

## Conflict Markers

When a conflict occurs, Git inserts markers into the file:

```
<<<<<<< HEAD
This is the version on the current branch
=======
This is the version from the incoming branch
>>>>>>> feature-branch
```

- `<<<<<<< HEAD` — Start of our version
- `=======` — Separator between versions
- `>>>>>>> feature-branch` — End of their version

## Resolving Conflicts

```bash
# 1. See which files have conflicts
git status

# 2. Open the conflicted files and edit them
#    Remove markers and keep the correct version

# 3. Mark as resolved
git add resolved-file.txt

# 4. Complete the merge
git commit
```

## Using Mergetool

```bash
# Configure a merge tool
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd "code --wait $MERGED"

# Launch mergetool for conflicted files
git mergetool

# View conflict diff
git diff

# View merge history
git log --merge
```

## Strategies

| Strategy | Command | Effect |
|----------|---------|--------|
| Keep ours | `git checkout --ours file.txt` | Accept current branch |
| Keep theirs | `git checkout --theirs file.txt` | Accept incoming branch |
| Manual edit | Edit file directly | Most control |
| Use mergetool | `git mergetool` | Visual diff tool |

## Conflict Resolution Workflow

```bash
# 1. See what's conflicted
git status

# 2. Pick a strategy
# For simple cases, accept one side:
git checkout --theirs src/feature.ts

# For complex cases, edit manually:
code src/feature.ts         # Remove markers, keep correct code

# 3. Mark resolved
git add src/feature.ts

# 4. Commit
git commit -m "Merge feature-branch: resolved conflicts in feature.ts"
```

## Abort a Merge

```bash
# Go back to before merge started
git merge --abort

# Go back to before rebase started
git rebase --abort
```

## Preventing Conflicts

- Pull/rebase frequently
- Communicate about large refactors
- Use smaller, focused commits
- Agree on code formatting standards

**Next**: [[Rebase]] — Reapply commits on top of another branch
