# Chapter 3: Pipes, Redirection & Text Processing

**Chapter 3** · ~30 min · Hands-on

> [!NOTE] Mission tie-in
> On a server, you rarely use one command alone. You chain them. Pipes and redirection are what make the Unix philosophy powerful: combine small tools to solve big problems. Log analysis, data extraction, automated reports — all built on these patterns.

## What You'll Learn

- Redirect output to files (`>`, `>>`)
- Pipe commands together (`|`)
- Process text with `cut`, `sort`, `uniq`, `wc`
- Combine tools to solve real problems

## Standard Streams

Every command has three streams:

| Stream | Number | Purpose |
|--------|--------|---------|
| `stdin` | 0 | Input (keyboard by default) |
| `stdout` | 1 | Output (screen by default) |
| `stderr` | 2 | Errors (screen by default) |

## Redirection

```bash
# Save output to a file (overwrite)
ls > files.txt

# Append output to a file
echo "new line" >> files.txt

# Redirect errors only
ls /nonexistent 2> error.log

# Redirect both stdout and stderr
ls /nonexistent &> all-output.log

# Suppress errors (send to /dev/null)
ls /nonexistent 2> /dev/null

# Read input from a file
sort < unsorted.txt
```

> [!TIP] `/dev/null` is a special file that discards everything written to it. Think of it as a black hole. Use `2>/dev/null` to silence noisy error messages.

## Pipes — The Superpower

Pipes (`|`) send the output of one command as input to another:

```bash
command1 | command2 | command3
```

Real examples:

```bash
# Count files in a directory
ls | wc -l

# Find the largest files
ls -lh | sort -k5 -rh | head -10

# See unique error types in a log
grep "ERROR" app.log | cut -d' ' -f3- | sort | uniq -c | sort -rn

# Show top 5 CPU-hungry processes
ps aux | sort -nrk 3 | head -5
```

## Essential Text Processing Tools

### cut — Extract Columns

```bash
cat data.csv
# name,age,email
# Alice,30,alice@example.com
# Bob,25,bob@example.com

cut -d',' -f1 data.csv          # Extract first field (name)
cut -d',' -f2,3 data.csv        # Extract fields 2 and 3
cut -d',' -f1-3 data.csv        # Extract fields 1 through 3

# Common: extract specific columns from logs
cat access.log | cut -d' ' -f1  # Get IP addresses from Apache log
```

### sort — Sort Lines

```bash
sort names.txt                  # Alphabetical sort
sort -r names.txt               # Reverse sort
sort -n numbers.txt             # Numeric sort
sort -rn numbers.txt            # Reverse numeric
sort -k2 -t',' data.csv         # Sort by 2nd field (comma separator)
sort -u names.txt               # Sort and remove duplicates
```

### uniq — Remove Duplicates

```bash
uniq file.txt                   # Remove adjacent duplicates
sort file.txt | uniq            # Remove ALL duplicates (sort first!)
sort file.txt | uniq -c         # Count occurrences
sort file.txt | uniq -d         # Show only duplicates
```

> [!WARNING] `uniq` only removes *adjacent* duplicates. Always pipe `sort` first if you want to find all unique lines.

### wc — Word Count

```bash
wc file.txt                     # Lines, words, characters
wc -l file.txt                  # Lines only
wc -w file.txt                  # Words only
wc -c file.txt                  # Bytes only
```

## Real-World Patterns

### Pattern 1: Log Analysis

```bash
# Top 10 IPs hitting your server
cat access.log | cut -d' ' -f1 | sort | uniq -c | sort -rn | head -10

# Most frequent 404 errors
grep " 404 " access.log | cut -d' ' -f7 | sort | uniq -c | sort -rn | head -10
```

### Pattern 2: Disk Usage Summary

```bash
# Largest directories
du -sh /var/log/* | sort -rh | head -10

# Total size of all .log files
find /var/log -name "*.log" | xargs du -ch | tail -1
```

### Pattern 3: Process Monitoring

```bash
# Most memory-hungry processes
ps aux | sort -nrk 4 | head -10

# Count running processes per user
ps aux | awk '{print $1}' | sort | uniq -c | sort -rn
```

## Hands-On Exercise

Open a terminal and run through these:

```bash
# 1. Create a sample log file
cat > ~/sample.log << EOF
2026-06-18 10:00:01 INFO  User alice logged in
2026-06-18 10:00:02 INFO  User bob logged in
2026-06-18 10:00:03 ERROR User alice failed payment
2026-06-18 10:00:04 INFO  User bob logged out
2026-06-18 10:00:05 ERROR User alice failed payment
2026-06-18 10:00:06 INFO  User charlie logged in
2026-06-18 10:00:07 ERROR User bob failed payment
EOF

# 2. Count lines
wc -l ~/sample.log

# 3. Find errors
grep "ERROR" ~/sample.log

# 4. Count errors per user
grep "ERROR" ~/sample.log | cut -d' ' -f5 | sort | uniq -c | sort -rn

# 5. Save analysis to a file
grep "ERROR" ~/sample.log > ~/errors.log

# 6. Cleanup
rm ~/sample.log ~/errors.log
```

## Self-Quiz

<details>
<summary><strong>1.</strong> What's the difference between <code>></code> and <code>>></code>?</summary>

`>` overwrites the file. `>>` appends to the file. Use `>>` when you want to add to existing content without losing it.
</details>

<details>
<summary><strong>2.</strong> How do you find the 5 most common lines in a file?</summary>

`sort file.txt | uniq -c | sort -rn | head -5` — sort, count occurrences, sort by count descending, take top 5.
</details>

<details>
<summary><strong>3.</strong> What command counts how many files are in the current directory?</summary>

`ls | wc -l` — list files and count the lines. (Note: this includes directories too. For files only: `ls -l | grep -v ^d | wc -l`)
</details>

<details>
<summary><strong>4.</strong> You want to suppress error messages from a command. How?</summary>

Append `2>/dev/null` to the command. For example: `rm file 2>/dev/null` silences "file not found" errors.
</details>

## Next Steps

- Apply the log analysis pattern on a real log file from your system
- Try building a pipeline that processes CSV data
- Move to [Chapter 4: Processes & System Monitoring](chapter4.md)

> [!NOTE] Ask followup questions!
> Stuck on a pipeline? Want to extract something specific from a file? Ask your teacher.

---

*Reference: [Linux Command Cheatsheet](reference/0001-command-cheatsheet.md)*
