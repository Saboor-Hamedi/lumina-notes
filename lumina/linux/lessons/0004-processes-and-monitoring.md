# Lesson 4: Processes & System Monitoring

**Lesson 4** · ~25 min · Hands-on

> [!NOTE] Mission tie-in
> When a server goes slow, something is eating CPU, memory, or disk. DevOps means knowing how to find what's wrong, kill what's broken, and monitor what matters. This lesson gives you those skills.

## What You'll Learn

- List and inspect running processes
- Understand CPU, memory, and disk usage
- Kill stuck processes
- Monitor system resources in real-time

## Processes Basics

Every program running on Linux is a **process**, identified by a **PID** (Process ID).

```bash
# See your own processes
ps

# See all processes on the system
ps aux
```

Breaking down `ps aux` output:

```
USER       PID  %CPU %MEM    VSZ   RSS STAT START   TIME COMMAND
root         1   0.0  0.3  102456  1234 Ss   10:00   0:02 /sbin/init
user      1234   0.5  2.1 567890  8901 S    10:05   0:15 /usr/bin/nginx
```

| Column | Meaning |
|--------|---------|
| `USER` | Who owns the process |
| `PID` | Process ID (use this to kill it) |
| `%CPU` | CPU usage percentage |
| `%MEM` | Memory usage percentage |
| `VSZ` | Virtual memory size |
| `RSS` | Actual physical memory used |
| `STAT` | Process state (S=sleeping, R=running, Z=zombie) |
| `COMMAND` | What command started it |

## Finding Specific Processes

```bash
# Find by name
ps aux | grep nginx
ps aux | grep python

# Find by PID
ps -p 1234

# Show process tree (parent-child relationships)
ps auxf

# Alternative: pgrep (returns PIDs only)
pgrep -u www-data nginx
```

## Real-Time Monitoring

### top — Interactive Viewer

```bash
top
```

Inside `top`:
- `q` — quit
- `1` — toggle per-CPU view
- `M` — sort by memory
- `P` — sort by CPU
- `k` — kill a process (enter PID)
- `u` — show processes for a user

```bash
# Install htop for a nicer view (if available)
# sudo apt install htop
htop
```

### Memory

```bash
free -h           # Show memory in human-readable format
#               total   used   free   shared   buff/cache   available
# Mem:          7.7G   2.1G   3.2G    120M       2.4G        5.2G
```

Key metric: look at `available` memory (not `free`). Available includes memory that can be reclaimed from cache.

### Disk

```bash
df -h             # Disk space usage per filesystem
du -sh /var/log   # Size of a specific directory
du -sh * | sort -rh | head -10   # Largest items in current dir
```

### Uptime & Load

```bash
uptime
# 10:30:00 up 3 days, 2:15, 2 users, load average: 0.08, 0.03, 0.01
```

Load average: 3 numbers (1 min, 5 min, 15 min). A load of 1.0 means 1 core is fully busy. On a 4-core CPU, load of 4.0 means all cores are busy. If the 15-min average is climbing, the system is under sustained pressure.

## Killing Processes

```bash
kill PID              # Graceful shutdown (SIGTERM) — asks process to stop
kill -9 PID           # Force kill (SIGKILL) — cannot be ignored
kill -15 PID          # Same as default kill

# Find and kill in one step
pkill nginx           # Kill all nginx processes by name
killall python        # Kill all python processes

# Safer version: only if running
pgrep nginx && kill $(pgrep nginx)
```

> [!WARNING] Use `kill -9` as a last resort. Always try plain `kill` first — it lets the process clean up. `kill -9` is like pulling the power cord.

## Background & Foreground

```bash
# Run a command in the background
sleep 100 &
# [1] 12345

# List background jobs
jobs
# [1]+  Running                 sleep 100 &

# Bring job to foreground
fg %1

# Suspend the current foreground job
# Press: Ctrl+Z

# Run in background and ignore hangups (survives terminal close)
nohup long-running-script.sh &
```

> [!TIP] Use `nohup` for scripts that should keep running after you log out. Combine with `&` to run in background: `nohup script.sh &`.

## Hands-On Exercise

```bash
# 1. List all processes
ps aux | head -20

# 2. Find top CPU users
ps aux | sort -nrk 3 | head -5

# 3. Find top memory users
ps aux | sort -nrk 4 | head -5

# 4. Check memory
free -h

# 5. Check disk
df -h

# 6. Check uptime
uptime

# 7. Run a background job
sleep 30 &
jobs
# Wait a few seconds, then:
fg %1
# (press Ctrl+C to cancel)

# 8. Monitor in real-time (run for 10 seconds, then q)
top -n 1 -b | head -20
```

## Self-Quiz

<details>
<summary><strong>1.</strong> How do you find the process using the most CPU?</summary>

`ps aux | sort -nrk 3 | head -5` — `-k 3` sorts by the 3rd column which is `%CPU`.
</details>

<details>
<summary><strong>2.</strong> What's the difference between <code>kill</code> and <code>kill -9</code>?</summary>

`kill` sends SIGTERM (15) — asks the process to shut down gracefully, allowing cleanup. `kill -9` sends SIGKILL — forces immediate termination, no cleanup possible. Try plain `kill` first.
</details>

<details>
<summary><strong>3.</strong> How do you keep a script running after you close the terminal?</summary>

`nohup script.sh &` — `nohup` ignores the hangup signal when the terminal closes, and `&` runs it in the background.
</details>

<details>
<summary><strong>4.</strong> What does a load average of 5.0 mean on an 8-core system?</summary>

The system is about 62% utilized (5.0 ÷ 8 cores). Not critical, but worth monitoring if it keeps rising. On a 2-core system, 5.0 would mean the system is overloaded.
</details>

## Next Steps

- Run `top` for a few minutes on your system and watch how processes behave
- Try `nohup` with a test script
- Move to [Lesson 5: Networking Essentials](0005-networking-essentials.md)

> [!NOTE] Ask followup questions!
> Not sure what a process is doing? Server running slow? Ask your teacher.

---

*Reference: [Linux Command Cheatsheet](../reference/0001-command-cheatsheet.md)*
