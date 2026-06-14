---
id: ab228c1c-1e3a-4552-bf15-68468944a17f
title: Dev Environment Setup
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781326822091
---

**Links**: [[Developer Workflow Automation]] | [[Shell Scripting]] | [[Package Managers]] | [[Build Tools]] | [[Vagrant]] | [[Ansible]]


# Dev Environment Setup

A well-configured development environment boosts productivity and reduces friction.

## Essential Tools

| Category | Tools |
|----------|-------|
| Editor | VS Code, Neovim, JetBrains |
| Terminal | Windows Terminal, iTerm2, Alacritty |
| Shell | PowerShell, zsh, bash |
| Version Control | Git + GitHub/GitLab |
| Package Manager | pip, npm, cargo, brew |

## Configuration Files

```json
// VS Code settings.json
{
  "editor.fontSize": 14,
  "editor.formatOnSave": true,
  "terminal.integrated.defaultProfile.windows": "PowerShell",
  "git.enableSmartCommit": true
}
```

## Version Managers

- **Python**: pyenv, uv
- **Node**: nvm, fnm
- **Rust**: rustup
- **Go**: gvm

Keep your dotfiles in a Git repository for easy setup across machines.

**See also**: [[Developer Workflow Automation]], [[Git Version Control]], [[Programming Resources]]
