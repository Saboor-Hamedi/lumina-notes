---
id: git-031-0000-0000-0000-000000000031
title: Git Server Hooks
language: markdown
tags: [git, hooks, server]
selection: null
isPinned: false
timestamp: 1781500000031
---
# Git Server Hooks

**Links**: [[Git Hooks]] | [[Git Push]] | [[Git Workflows]] | [[Git Best Practices]]

## What are Server Hooks?

Server hooks run on the remote repository (e.g., GitHub, GitLab, or your own Git server) in response to push events.

## Pre-Receive Hook

Runs before any refs are updated. Can reject the entire push.

```bash
#!/bin/sh
# Reject commits without valid commit messages
while read oldrev newrev refname; do
    for commit in $(git rev-list $oldrev..$newrev); do
        msg=$(git log -1 --pretty=%B $commit)
        if ! echo "$msg" | grep -qE '^(feat|fix|docs|refactor|test|chore)\('; then
            echo "Commit $commit: message must follow conventional commits"
            exit 1
        fi
    done
done
```

## Update Hook

Similar to pre-receive but runs once per ref being updated.

```bash
#!/bin/sh
# Protect main branch from direct pushes
refname="$1"
if [ "$refname" = "refs/heads/main" ]; then
    echo "Direct pushes to main are not allowed. Use pull requests."
    exit 1
fi
```

## Post-Receive Hook

Runs after refs are updated. Used for CI/CD triggers, deployments, notifications.

```bash
#!/bin/sh
# Trigger CI pipeline
while read oldrev newrev refname; do
    curl -X POST "https://ci.example.com/build?branch=${refname#refs/heads/}"
done
```

## Hosted Git Services

| Service | Server Hooks Alternative |
|---------|--------------------------|
| GitHub | GitHub Actions, Branch Protection Rules |
| GitLab | GitLab CI/CD, Protected Branches |
| Bitbucket | Bitbucket Pipelines, Branch Restrictions |

On hosted services, you typically use their built-in branch protection settings rather than writing server hooks manually:
- Require PRs before merging to main
- Require status checks to pass
- Require signed commits
- Limit who can push to branches

**Next**: [[Git LFS]] — Large file storage