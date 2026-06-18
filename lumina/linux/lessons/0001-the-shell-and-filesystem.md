# Lesson 1: The Linux Shell & Filesystem

**Lesson 1** · ~25 min · Hands-on

> [!NOTE] Mission tie-in
> Every server you manage, every DevOps pipeline you build, every deployment you run — it all starts in the shell. This lesson builds the foundation you'll use in every session after.

## What You'll Learn

- What the shell is and how to get one on Windows
- The Linux filesystem layout
- Navigate with `pwd`, `ls`, `cd`
- Understand command syntax: `command [options] [arguments]`
- Get help with `man` and `--help`

## Getting a Linux Shell on Windows

You're on Windows, so we need a Linux environment. Pick one:

**Option A — Git Bash (easiest, included with Git for Windows)**
```bash
# Search "Git Bash" in Start Menu — opens a bash-like terminal
```

**Option B — WSL (best for real Linux learning)**
```powershell
# In PowerShell as Admin:
wsl --install -d Ubuntu
# Then launch "Ubuntu" from Start Menu
```

**Option C — Docker (if you already have Docker)**
```bash
docker run -it --rm ubuntu:latest bash
```

> [!TIP] I recommend WSL — it's a real Linux kernel running inside Windows. You'll use actual Linux commands, not emulated ones. For this course, Git Bash also works fine for lessons 1-3.

## The Shell Prompt

When you open a terminal, you'll see something like:

```
user@hostname:~$
```

Breaking it down:

| Part | Meaning |
|------|---------|
| `user` | Your username |
| `hostname` | The machine name |
| `~` | Current directory (`~` = your home folder) |
| `$` | Running as a normal user (`#` would mean root) |

Type your first command:

```bash
whoami
```

It prints your username. Simple — you tell the shell to run a program, it runs it and shows the output.

## The Linux Filesystem

Unlike Windows (with `C:\`, `D:\`), Linux has a single tree starting at `/` (root):

```
/                    ← root (everything starts here)
├── bin/             ← essential user commands (ls, cp, mv)
├── boot/            ← kernel and boot files
├── dev/             ← device files (disks, USBs)
├── etc/             ← system configuration files
├── home/            ← users' personal folders
│   └── user/        ← your home directory (~)
├── root/            ← home for the root user
├── tmp/             ← temporary files (cleared on reboot)
├── usr/             ← user-installed programs and docs
│   ├── bin/         ← most user commands
│   └── local/       ← manually installed software
└── var/             ← variable data (logs, databases)
    └── log/         ← system log files
```

> [!TIP] Don't memorize this. You'll learn it naturally as you use the system. Just know that `/` is the root and `~` is your home.

## Navigate the Filesystem

### pwd — Where am I?

```bash
pwd
# Output: /home/user
```

Stands for **p**rint **w**orking **d**irectory. Tells you your current location.

### ls — What's here?

```bash
ls                    # List files and folders
ls -l                 # Long format (permissions, size, date)
ls -a                 # Show hidden files (start with .)
ls -la                # Combine: long + all
ls -lh                # Long + human-readable sizes
```

Try each one in your home directory and notice the difference.

### cd — Go somewhere

```bash
cd /etc               # Go to /etc
cd                    # Go home (no args = home)
cd ~                  # Same — go home
cd ..                 # Go up one level (parent directory)
cd ../..              # Go up two levels
cd -                  # Go back to previous directory
```

> [!TIP] `cd` with no arguments always takes you home. This is one of the most useful shortcuts.

## Command Structure

Most commands follow this pattern:

```bash
command [options] [arguments]
```

| Part | Example | Meaning |
|------|---------|---------|
| Command | `ls` | The program to run |
| Options | `-la` | Modify behavior (short: `-l`, long: `--all`) |
| Arguments | `/etc` | What the command acts on |

Options can be combined: `ls -l -a` = `ls -la` = `ls -a -l`.

Long options use `--`:
```bash
ls --all              # Same as ls -a
ls --human-readable   # Same as ls -h
```

## Getting Help

```bash
command --help        # Quick usage summary
man command           # Full manual page (press q to quit)
```

Try it:
```bash
ls --help
man ls
```

> [!TIP] `man` is your best friend. Inside a man page: use arrow keys to scroll, type `/searchterm` to search, press `n` for next match, press `q` to quit.

## Your First Exercise

Open your terminal and run each of these. Type them — don't copy-paste. Muscle memory matters.

```bash
# 1. Where are you?
pwd

# 2. What's in your home?
ls -la ~

# 3. Explore the filesystem
cd /
ls -la
cd /etc
ls | head -20
cd /var/log
ls

# 4. Go home
cd

# 5. Read about a command
man ls
```

<details>
<summary><strong>What did you see in /etc?</strong> (peek after trying)</summary>

`/etc` contains system configuration files. You probably saw `passwd` (user accounts), `hostname` (machine name), `ssh/` (SSH config), and more. These are plain text files — you can read them with `cat` later.
</details>

## Self-Quiz

<details>
<summary><strong>1.</strong> What does <code>pwd</code> do?</summary>

Prints the current working directory — tells you where you are in the filesystem.
</details>

<details>
<summary><strong>2.</strong> What's the difference between <code>ls</code> and <code>ls -a</code>?</summary>

`ls` shows visible files only. `ls -a` also shows hidden files (those starting with a dot, like `.bashrc`).
</details>

<details>
<summary><strong>3.</strong> How do you go to your home directory from anywhere?</summary>

`cd` (with no arguments) or `cd ~` — both take you to your home directory.
</details>

<details>
<summary><strong>4.</strong> How do you get help for the <code>cp</code> command?</summary>

`cp --help` for a quick summary, or `man cp` for the full manual. Press `q` to exit the manual.
</details>

## Next Steps

1. If you don't have a Linux shell yet, install WSL or Git Bash
2. Spend 5 minutes exploring `/`, `/etc`, `/var/log`, and `/tmp`
3. Read `man` pages for `ls`, `cd`, `pwd` — just browse them
4. When comfortable, move to [Lesson 2: Working with Files & Permissions](0002-files-and-permissions.md)
5. Then continue through: [Lesson 3](0003-pipes-redirection.md) → [Lesson 4](0004-processes-and-monitoring.md) → [Lesson 5](0005-networking-essentials.md)

> [!NOTE] Ask followup questions!
> Got stuck setting up WSL? Confused about something in the filesystem? Ask your teacher anytime.

---

*Reference: [Linux Command Cheatsheet](../reference/0001-command-cheatsheet.md) | [ExplainShell.com](https://explainshell.com/)*
