---
id: git-045-0000-0000-0000-000000000045
title: Git Best Practices
language: markdown
tags: [git, best-practices]
selection: null
isPinned: false
timestamp: 1781500000045
---
# Git Best Practices

**Links**: [[Git Overview]] | [[Git Commit]] | [[Git Branch]] | [[Git Workflows]] | [[Git Hooks]] | [[Git Pull Requests]] | [[Git Merge]] | [[Git Rebase]]

## Commit Practices

- **Small, logical commits**: One commit per logical change
- **Imperative messages**: "Fix bug" not "Fixed bug"
- **Reference issues**: `Closes #42`, `Refs JIRA-456`
- **Don't commit half-broken code**: Each commit should pass tests
- **No secret data**: Never commit passwords, tokens, or keys

## Branch Strategy

- **Short-lived branches**: Merge within days, not weeks
- **Consistent naming**: `feature/`, `bugfix/`, `hotfix/`, `release/`
- **Delete after merge**: `git branch -d feature-name`
- **Protect main**: Require PRs, status checks, reviews

## Collaboration

- **Pull before push**: Always `git pull --rebase` before pushing
- **Rebase local, merge published**: Rebase unpushed commits, merge shared ones
- **Communicate force pushes**: Announce if you must force push
- **Small PRs**: Under 400 lines is ideal for thorough review

## Safety

- **Never force push to shared branches**: Use `--force-with-lease` if you must
- **Backup before destructive operations**: `git stash` or `git branch` as backup
- **Use reflog**: It's your safety net for recovery
- **Run `git clean -n` first**: Preview before cleaning

## Repository Hygiene

- **Keep `.gitignore` up to date**: No build artifacts in repo
- **Run `git gc` periodically**: Keeps repo performant
- **Check size**: Avoid bloating with large binary files (use LFS)
- **Sign your commits**: GPG signing proves authorship

## The Golden Rules

1. One commit = one logical change
2. Commit messages explain *why*, not *what*
3. Never rewrite public history
4. Review before merging
5. Main should always be deployable

**Next**: [[Git Overview]] — Start from the beginning