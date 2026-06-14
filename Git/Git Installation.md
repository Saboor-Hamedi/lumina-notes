---
id: git-002-0000-0000-0000-000000000002
title: Git Installation
language: markdown
tags: [git, setup]
selection: null
isPinned: false
timestamp: 1781500000002
---
# Git Installation

**Links**: [[Git Overview]] | [[Git Configuration]] | [[Git Init and Clone]] | [[Git Add and Status]] | [[Git Commit]] | [[Git Branch]] | [[Git Remote]]

## Installing Git

### Windows
Download from [git-scm.com](https://git-scm.com) and run the installer (`.exe`). Recommended options:
- Use Git from Git Bash or command line
- Use bundled OpenSSH
- Checkout Windows-style, commit Unix-style line endings
- Use MinTTY terminal
- Enable file system caching

```bash
git --version
# git version 2.45.0.windows.1
```

### macOS
```bash
brew install git          # Via Homebrew
xcode-select --install    # Via Xcode CLT
git --version
```

### Linux
```bash
sudo apt install git      # Debian/Ubuntu
sudo dnf install git      # Fedora/RHEL
sudo pacman -S git        # Arch
git --version
```

## First Run Setup

After installing, configure your identity:
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

**Next**: [[Git Configuration]] — Set up Git properly