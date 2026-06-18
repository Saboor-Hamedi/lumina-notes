# Linux Command Cheatsheet

**Reference** · Quick lookup for common commands

## Navigation

| Command | What it does |
|---------|-------------|
| `pwd` | Print working directory (where am I?) |
| `ls` | List files and directories |
| `ls -la` | List all files (including hidden) with details |
| `ls -lh` | List with human-readable sizes |
| `cd` | Go home (no arguments) |
| `cd /path` | Go to /path |
| `cd ..` | Go up one level |
| `cd -` | Go back to previous directory |

## File Operations

| Command | What it does |
|---------|-------------|
| `cp src dst` | Copy file |
| `cp -r src dst` | Copy directory recursively |
| `mv src dst` | Move or rename file |
| `rm file` | Delete file (permanent!) |
| `rm -r dir` | Delete directory recursively |
| `rm -f file` | Force delete (no confirmation) |
| `mkdir dir` | Create directory |
| `mkdir -p a/b/c` | Create nested directories |
| `touch file` | Create empty file or update timestamp |

## Viewing Files

| Command | What it does |
|---------|-------------|
| `cat file` | Print entire file |
| `less file` | Scroll through file (q to quit) |
| `head file` | First 10 lines |
| `head -n 20 file` | First 20 lines |
| `tail file` | Last 10 lines |
| `tail -f file` | Follow file as it grows (logs) |

## Getting Help

| Command | What it does |
|---------|-------------|
| `cmd --help` | Quick usage summary |
| `man cmd` | Full manual (q to quit) |

## Permissions

| Command | What it does |
|---------|-------------|
| `ls -l` | Show file permissions |
| `chmod 755 file` | Set permissions (rwxr-xr-x) |
| `chmod +x file` | Make executable |
| `chown user:group file` | Change owner/group |

## Processes

| Command | What it does |
|---------|-------------|
| `ps aux` | List all running processes |
| `top` | Interactive process viewer (q to quit) |
| `kill PID` | Stop a process by ID |
| `kill -9 PID` | Force kill a process |
| `htop` | Nicer process viewer (install first) |

## Text Processing

| Command | What it does |
|---------|-------------|
| `grep pattern file` | Search for pattern in file |
| `grep -r pattern dir` | Search recursively in directory |
| `grep -i pattern file` | Case-insensitive search |
| `sort file` | Sort lines alphabetically |
| `sort -n file` | Sort numerically |
| `uniq file` | Remove adjacent duplicates |
| `wc file` | Count lines, words, characters |
| `cut -d, -f1 file` | Extract first field of CSV |

## Pipes and Redirection

| Symbol | What it does |
|--------|-------------|
| `cmd1 \| cmd2` | Pipe output of cmd1 into cmd2 |
| `cmd > file` | Redirect output to file (overwrite) |
| `cmd >> file` | Redirect output to file (append) |
| `cmd 2> file` | Redirect stderr to file |
| `cmd < file` | Read input from file |

## System Info

| Command | What it does |
|---------|-------------|
| `uname -a` | Full system info |
| `df -h` | Disk usage (human-readable) |
| `du -sh dir` | Directory size |
| `free -h` | Memory usage |
| `uptime` | How long the system has been running |
| `whoami` | Current username |
| `id` | User ID and group info |

## Shortcuts

| Keys | What it does |
|------|-------------|
| `Ctrl+C` | Kill current command |
| `Ctrl+D` | Exit shell / EOF |
| `Ctrl+Z` | Suspend current command |
| `Ctrl+R` | Search command history |
| `Tab` | Auto-complete command or path |
| `↑ ↓` | Browse command history |
| `!!` | Repeat last command |
| `!$` | Last argument of previous command |

---

*Use [ExplainShell.com](https://explainshell.com/) to decode any command you don't understand.*
