# Linux Command Line Glossary

Canonical terminology for this teaching workspace.

## Shell

**Shell**:
A program that interprets commands and runs them. The most common is Bash (Bourne Again SHell). The shell presents a prompt, reads your input, and executes the command.

**Bash (Bourne Again SHell)**:
The default shell on most Linux distributions. Your scripts should start with `#!/bin/bash`.

**Prompt**:
The text shown before the cursor in a terminal. Typically `user@hostname:~$`. The `$` means normal user, `#` means root.

**Exit Code**:
A number returned by every command. 0 = success, any non-zero = error. Access with `$?` right after the command.

**Shebang**:
`#!/bin/bash` at the top of a script. Tells the system which interpreter to run the script with.

## Filesystem

**Root (`/`)**:
The top of the Linux filesystem tree. Everything is under `/`. Unlike Windows, there are no drive letters.

**Home Directory (`~`)**:
Each user's personal folder. `~` is shorthand. Typically `/home/username`. `cd` with no arguments goes here.

**Hidden File**:
A file whose name starts with a dot (`.`). Hidden from `ls` unless you use `ls -a`. Examples: `.bashrc`, `.gitignore`.

**Permission / Mode**:
Read (`r`), write (`w`), execute (`x`) for owner, group, and others. Displayed by `ls -l`. Numeric: `4+2+1 = 7` (rwx), `4+0+1 = 5` (r-x), etc.

**Symbolic Link (symlink)**:
A file that points to another file. Created with `ln -s`. Like a shortcut, but more powerful.

## I/O Streams

**stdin (0)**:
Standard input — where a command reads input from. Keyboard by default.

**stdout (1)**:
Standard output — where a command writes normal output. Screen by default.

**stderr (2)**:
Standard error — where a command writes error messages. Screen by default.

**Pipe (`|`)**:
Sends the stdout of one command into the stdin of another. The core of the Unix philosophy: `command1 | command2 | command3`.

**Redirection (`>`, `>>`)**:
Send output to a file instead of the screen. `>` overwrites the file. `>>` appends to the file.

**/dev/null**:
A special file that discards everything written to it. The "black hole." Use `2>/dev/null` to silence errors.

## Processes

**PID (Process ID)**:
A unique number assigned to every running process. Use it with `kill`, `ps -p`, etc.

**SIGTERM (signal 15)**:
The default signal sent by `kill`. Asks the process to shut down gracefully. The process can clean up resources.

**SIGKILL (signal 9)**:
`kill -9`. Force kills the process immediately. The process cannot catch or ignore this. Last resort.

**Foreground / Background**:
A process running in the foreground occupies the terminal. Add `&` to run in the background. `fg` brings it back. `jobs` lists background jobs.

**nohup**:
Run a command immune to hangups. If you close the terminal, the process keeps running. Use with `&`.

**Load Average**:
Three numbers (1, 5, 15 min) showing CPU demand. 1.0 = one core fully busy. On a 4-core system, 4.0 = all cores busy.

## System

**Package Manager (apt)**:
A tool for installing, updating, and removing software. On Ubuntu/Debian: `apt`. On RHEL/Fedora: `dnf`.

**systemd**:
The init system and service manager on most modern Linux distros. Use `systemctl` to control services.

**systemctl**:
Command to manage systemd services. `start`, `stop`, `restart`, `enable`, `disable`, `status`.

**journalctl**:
Command to view logs collected by systemd's journal. `journalctl -u nginx` shows logs for nginx.

**sudo**:
Run a command as another user (usually root). `sudo apt install nginx`. Users need to be in the `sudo` group.

## Networking

**curl**:
Command-line HTTP client. Download files, test APIs, check headers. `curl -I https://example.com` shows response headers.

**wget**:
Simple file downloader. Supports recursive downloads and resuming interrupted transfers.

**ping**:
Test if a remote host is reachable and measure round-trip time. `ping -c 4 google.com` sends 4 pings.

**ss (Socket Statistics)**:
Show network connections and listening ports. `ss -tuln` shows all listening TCP/UDP ports.

**traceroute**:
Show every network hop between you and a destination. Useful for diagnosing where connectivity breaks.

## Text Processing

**grep**:
Search for patterns in text. `grep "error" log.txt` finds lines containing "error". `-r` for recursive, `-i` for case-insensitive.

**cut**:
Extract columns from delimited text. `cut -d',' -f1 data.csv` gets the first column of a CSV.

**sort**:
Sort lines alphabetically or numerically. `-n` for numeric sort, `-r` for reverse, `-k` for key column.

**uniq**:
Remove or count duplicate lines. Only removes adjacent duplicates — always pipe `sort` first. `-c` counts occurrences.

**wc**:
Word count. `-l` counts lines, `-w` counts words, `-c` counts bytes.
