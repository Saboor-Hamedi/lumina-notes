---
id: new-009-0000-0000-0000-000000000009
title: Command Line Productivity
language: markdown
tags: [devops, monitoring]
selection: null
isPinned: false
timestamp: 1781600000009
---

# Command Line Productivity

**Links**: [[Dev Environment Setup]] | [[Git Aliases]] | [[Developer Workflow Automation]] | [[Docker Containers]] | [[Environment Variables]]

## Essential Commands

### File Operations

```bash
ls -la                       # List all files with details
find . -name "*.py"          # Find files by name
grep -r "function" src/      # Search content recursively
tree -L 2                    # Show directory tree (depth 2)
du -sh *                     # Show disk usage per item
```

### Process Management

```bash
ps aux                       # List all processes
top                          # Live process viewer
kill -9 PID                  # Force kill a process
htop                         # Better top (install first)
```

### Text Processing

```bash
# Pipe chains
cat file.log | grep "ERROR" | sort | uniq -c | sort -nr

# Stream editing
sed -i 's/old/new/g' file.txt
awk '{print $1, $3}' data.csv

# JSON processing (jq)
cat data.json | jq '.users[] | {name, email}'
```

## Shell Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+R` | Reverse search history |
| `Ctrl+E` | Go to end of line |
| `Ctrl+A` | Go to start of line |
| `Ctrl+W` | Delete word backward |
| `Ctrl+U` | Delete entire line |
| `Ctrl+K` | Delete to end of line |
| `Ctrl+Y` | Paste deleted text |
| `Ctrl+L` | Clear screen |
| `!!` | Repeat last command |
| `!$` | Last argument of last command |
| `!$:p` | Print last argument without executing |
| `!npm` | Run last npm command |

## Custom Prompt

```bash
# In .bashrc or .zshrc
export PS1="\u@\h \w \$ "
# Result: user@hostname ~/project $

# With Git branch (bash)
parse_git_branch() {
  git branch 2>/dev/null | sed -n 's/* \(.*\)/ (\1)/p'
}
export PS1="\u@\h \w\$(parse_git_branch) \$ "
```

## Multiplexers

```bash
tmux new -s mysession        # Create session
tmux detach                  # Detach (Ctrl+B d)
tmux attach -t mysession     # Reattach
tmux list-sessions           # List all sessions
tmux kill-session -t mysess  # Kill a session
```

## Modern Alternatives

| Old | New | Benefit |
|-----|-----|---------|
| cat | bat | Syntax highlighting, line numbers |
| ls | exa / eza | Colors, icons, tree view |
| find | fd | Faster, sensible defaults |
| grep | ripgrep (rg) | Blazing fast, git-aware |
| sed/awk | sd | Simpler string replacement |
| du | dust | Interactive disk usage |
| top | htop / btm | Interactive process view |
| history | fzf | Fuzzy search on history |

## Productivity Tips

- Use `Ctrl+R` with fzf for even better history search
- Install `thefuck` to auto-correct mistyped commands
- Use `z` or `zoxide` for quick directory jumping
- Set up `bat` as the default pager for man pages
- Use `tldr` instead of man for simplified examples

- Use `tldr` instead of man for simplified examples

**Next**: [[Database Security]] — Secure your data
