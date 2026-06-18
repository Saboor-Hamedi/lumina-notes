# Lesson 2: Working with Files & Permissions

**Lesson 2** · ~25 min · Hands-on

> [!NOTE] Mission tie-in
> On any Linux server, everything is a file — configs, logs, devices, even running processes. Knowing how to manage files and their permissions is essential for DevOps work: deploying code, securing configs, debugging issues.

## What You'll Learn

- Copy, move, delete, and link files
- Understand file permissions (rwx)
- Change permissions with `chmod` and ownership with `chown`
- Read and search files with `cat`, `less`, `head`, `tail`, `grep`

## File Operations

### Copy — cp

```bash
cp file.txt backup.txt          # Copy file
cp -r docs/ docs-backup/        # Copy directory recursively
cp -i file.txt backup.txt       # Ask before overwriting
cp -v file.txt backup.txt       # Verbose — show what's being copied
```

> [!TIP] `cp -i` is your friend. It saves you from accidentally overwriting files. Alias it in your `.bashrc`.

### Move / Rename — mv

```bash
mv file.txt /tmp/               # Move file to /tmp
mv oldname.txt newname.txt      # Rename file
mv -i file.txt /tmp/            # Ask before overwriting
```

### Delete — rm

```bash
rm file.txt                     # Delete file (permanent — no trash!)
rm -r docs/                     # Delete directory and contents
rm -f file.txt                  # Force delete (no confirmation)
rm -rf docs/                    # ⚠️ Nuclear option — be careful!
```

> [!WARNING] There is no trash bin in the terminal. `rm` deletes files permanently. There is no undo. Double-check before you hit Enter.

### Create Directories — mkdir

```bash
mkdir projects                  # Create single directory
mkdir -p projects/web/app       # Create nested directories (all at once)
```

### Create Empty Files — touch

```bash
touch file.txt                  # Create empty file
touch existing.txt              # Update timestamp (doesn't erase content)
```

## Viewing Files

```bash
cat file.txt                    # Print entire file (good for small files)
less file.txt                   # Scroll through (q to quit)
head file.txt                   # First 10 lines
head -n 20 file.txt             # First 20 lines
tail file.txt                   # Last 10 lines
tail -n 50 file.txt             # Last 50 lines
tail -f app.log                 # Follow file as it grows (hit Ctrl+C to stop)
```

> [!TIP] `tail -f` is indispensable for watching logs in real-time. When debugging a server issue, this is often your first command.

## Searching Files with grep

```bash
grep "error" app.log                    # Find lines containing "error"
grep -i "error" app.log                 # Case-insensitive search
grep -r "TODO" src/                     # Search recursively in directory
grep -n "error" app.log                 # Show line numbers
grep -c "error" app.log                 # Count matches
grep -v "debug" app.log                 # Invert — show lines WITHOUT "debug"
grep "error" *.log                      # Search all .log files in current dir
```

## Hands-On: File Permissions

Every file and directory has permissions. Run `ls -l`:

```bash
ls -l
# -rw-r--r-- 1 user group 1024 Jun 18 10:00 file.txt
# drwxr-xr-x 2 user group 4096 Jun 18 09:00 docs/
```

Breaking it down:

```
- r w - r - - r - -
│ │ │   │   │
│ │ │   │   └── Others (everyone else)
│ │ │   └────── Group (users in the file's group)
│ │ └────────── Owner (the file's user)
│ └──────────── Read / Write / Execute
└─────────────── File type (-=file, d=directory, l=symlink)
```

Each position is a bit: `r`=read, `w`=write, `x`=execute, `-`=no permission.

### Understanding rwx on Files vs Directories

| Permission | On a File | On a Directory |
|-----------|-----------|----------------|
| `r` | Read contents | List files (ls) |
| `w` | Modify contents | Create/delete files inside |
| `x` | Execute as program | Enter directory (cd) |

### Changing Permissions — chmod

```bash
chmod u+x script.sh             # Add execute for owner
chmod g-w file.txt              # Remove write for group
chmod o+r file.txt              # Add read for others
chmod a+x script.sh             # Add execute for everyone (a=all)
```

Using numeric mode (most common):

```
r=4, w=2, x=1 → sum them
755 = rwxr-xr-x (owner: all, group/others: rx)
644 = rw-r--r-- (owner: rw, group/others: r)
700 = rwx------ (owner: all, nobody else)
```

```bash
chmod 755 script.sh             # Make executable for everyone
chmod 644 file.txt              # Owner can write, others can read
chmod 600 ssh-key.pem           # Owner only — used for SSH keys
```

> [!WARNING] Never set `chmod 777` on files. It means anyone can modify them. On servers, this is a security risk.

### Changing Ownership — chown

```bash
chown user file.txt             # Change owner
chown user:group file.txt       # Change owner and group
chown -R user:group docs/       # Recursive — entire directory tree
```

> [!TIP] Most of the time you'll use `chmod 755` for scripts, `chmod 644` for config files, and `chmod 600` for SSH keys and secrets.

## Hands-On Exercise

Open a terminal and run through these:

```bash
# 1. Create a workspace
mkdir -p ~/linux-practice && cd ~/linux-practice

# 2. Create files
touch readme.md script.sh data.csv
echo "hello world" > readme.md
echo "#!/bin/bash\necho 'running...'" > script.sh

# 3. View files
cat readme.md
head -3 data.csv
ls -l

# 4. Change permissions
chmod 755 script.sh
chmod 644 readme.md
ls -l

# 5. Search with grep
grep -r "hello" ~/linux-practice

# 6. Copy and move
cp readme.md readme-backup.md
mv data.csv /tmp/
rm readme-backup.md

# 7. Cleanup
cd ~ && rm -rf ~/linux-practice
```

## Self-Quiz

<details>
<summary><strong>1.</strong> What does <code>chmod 644</code> do?</summary>

Sets `rw-r--r--` — owner can read/write, group and others can only read. Standard for files.
</details>

<details>
<summary><strong>2.</strong> What's the difference between <code>rm file</code> and <code>rm -rf dir</code>?</summary>

`rm file` deletes a single file. `rm -rf dir` deletes a directory and ALL its contents — files, subdirectories, everything — without asking. Use `-rf` with extreme care.
</details>

<details>
<summary><strong>3.</strong> How do you search for "TODO" in all files under <code>src/</code>, showing line numbers?</summary>

`grep -rn "TODO" src/` — `r` for recursive, `n` for line numbers.
</details>

<details>
<summary><strong>4.</strong> Why should you never use <code>chmod 777</code>?</summary>

Because it gives read, write, and execute permissions to everyone. Anyone on the system can modify or execute that file. On a server, that's a security hole.
</details>

## Next Steps

- Practice `chmod` on test files until the numeric modes feel natural (755, 644, 600)
- Use `grep` on your own files for a week — search code, logs, anything
- Move to [Lesson 3: Pipes, Redirection & Text Processing](0003-pipes-redirection.md)

> [!NOTE] Ask followup questions!
> Permissions confusing? Forgot what `chmod 755` means? Ask your teacher.

---

*Reference: [Linux Command Cheatsheet](../reference/0001-command-cheatsheet.md)*
