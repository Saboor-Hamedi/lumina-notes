---
id: git-038-0000-0000-0000-000000000038
title: Git Aliases
language: markdown
tags: [git, aliases]
selection: null
isPinned: false
timestamp: 1781500000038
---
# Git Aliases

**Links**: [[Git Configuration]] | [[Git Log]] | [[Git Diff]] | [[Git Branch]] | [[Git Best Practices]]

## What are Aliases?

Git aliases are custom shorthand commands you define. Instead of typing `git commit --all --message`, type `git cm`.

## Basic Aliases

```bash
# Set aliases via git config
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.df diff

# Usage
git st    # = git status
git co    # = git checkout
```

## Advanced Aliases

```bash
# Log with graph
git config --global alias.lg "log --oneline --graph --decorate --all"

# Pretty log
git config --global alias.pl "log --pretty=format:'%C(yellow)%h%Creset %s %Cgreen(%cr)%Creset %C(blue)%an%Creset'"

# Last commit
git config --global alias.last "log -1 HEAD"

# Unstage file
git config --global alias.unstage "reset HEAD --"

# Visual diff
git config --global alias.vdiff "difftool --tool=vimdiff"

# Show contributions
git config --global alias.contrib "shortlog --summary --numbered"
```

## Aliases with Arguments

```bash
# Aliases can include arguments with !
git config --global alias.undo "!git reset --soft HEAD~1"
git config --global alias.amend "!git add -A && git commit --amend --no-edit"
git config --global alias.wip "!git add -A && git commit -m WIP"
```

## External Commands

Prefix with `!` to run non-git commands:

```bash
# Show repo root
git config --global alias.root "!pwd"

# Open in GitHub
git config --global alias.open "!gh repo view --web"
```

## My Recommended Aliases

```bash
git config --global alias.lg "log --oneline --graph --all --decorate"
git config --global alias.undo "reset --soft HEAD~1"
git config --global alias.unstage "restore --staged"
git config --global alias.last "log -1 --stat"
git config --global alias.contrib "shortlog --summary --numbered --all"
git config --global alias.cleanup "!git branch --merged | grep -v '\\*\\|main\\|develop' | xargs -r git branch -d"
```

**Next**: [[Git Workflows]] — Development workflows