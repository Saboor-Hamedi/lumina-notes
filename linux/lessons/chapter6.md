---
id: 0006-shell-scripting
title: 0006-shell-scripting
language: markdown
tags: ''
selection: null
isPinned: false
customIcon: null
timestamp: 1781834651322.4685
---
# Chapter 6: Shell Scripting

**Chapter 6** · ~35 min · Hands-on

> [!NOTE] Mission tie-in
> Shell scripting is where you stop typing commands and start automating. A deployment script, a backup cron job, a log rotator, a health check — these are all shell scripts. This is the skill that makes you productive on any Linux server.

## What You'll Learn

- Create and run a shell script
- Variables, conditionals, and loops
- Read command-line arguments
- Write a practical backup script

## Your First Script

```bash
#!/bin/bash
# A comment: this script prints a greeting
echo "Hello, $USER!"
echo "Today is $(date +%A)"
echo "You are in $(pwd)"
```

Save as `hello.sh`, then:

```bash
chmod +x hello.sh
./hello.sh
```

The `#!/bin/bash` is the **shebang** — it tells the system which interpreter to use. Always start scripts with it.

## Variables

```bash
#!/bin/bash

# Assign (no spaces around =!)
NAME="Alice"
AGE=30

# Use with $
echo "$NAME is $AGE years old"

# Command substitution — capture command output
FILES=$(ls | wc -l)
echo "Files in this directory: $FILES"

# Arithmetic
TOTAL=$((AGE + 10))
echo "In 10 years: $TOTAL"

# Quotes matter
NAME="Alice Smith"
echo $NAME      # Splits into two words — bad
echo "$NAME"    # Keeps as one word — good
```

> [!TIP] Always quote variables with `"$VAR"` unless you specifically need word splitting. Unquoted variables with spaces will break.

## Conditionals

```bash
#!/bin/bash

FILE="test.txt"

if [ -f "$FILE" ]; then
    echo "$FILE exists"
else
    echo "$FILE does not exist"
fi

# Numeric comparison
SCORE=85
if [ "$SCORE" -ge 90 ]; then
    echo "Grade: A"
elif [ "$SCORE" -ge 80 ]; then
    echo "Grade: B"
else
    echo "Grade: C"
fi
```

### Common Test Expressions

| Expression | True if |
|-----------|---------|
| `[ -f "$FILE" ]` | File exists and is a regular file |
| `[ -d "$DIR" ]` | Directory exists |
| `[ -z "$STR" ]` | String is empty |
| `[ -n "$STR" ]` | String is not empty |
| `[ "$A" = "$B" ]` | Strings are equal |
| `[ "$A" -eq "$B" ]` | Numbers are equal |
| `[ "$A" -gt "$B" ]` | Number A > Number B |

## Loops

```bash
#!/bin/bash

# For loop over a list
for fruit in apple banana cherry; do
    echo "I like $fruit"
done

# For loop over files
for file in *.txt; do
    echo "Processing $file"
    wc -l "$file"
done

# While loop — read a file line by line
while IFS= read -r line; do
    echo "Line: $line"
done < input.txt

# While with counter
COUNT=0
while [ "$COUNT" -lt 5 ]; do
    echo "Count: $COUNT"
    COUNT=$((COUNT + 1))
done
```

## Functions

```bash
#!/bin/bash

# Define a function
log_info() {
    echo "[INFO] $(date '+%Y-%m-%d %H:%M:%S') - $1"
}

log_error() {
    echo "[ERROR] $(date '+%Y-%m-%d %H:%M:%S') - $1" >&2
}

# Use it
log_info "Script started"
log_error "Something went wrong"
```

## Command-Line Arguments

```bash
#!/bin/bash

echo "Script name: $0"
echo "First arg: $1"
echo "Second arg: $2"
echo "All args: $@"
echo "Number of args: $#"
```

Run it: `./script.sh foo bar` → shows `foo` and `bar`.

```bash
#!/bin/bash

if [ $# -eq 0 ]; then
    echo "Usage: $0 <filename>"
    exit 1
fi

FILE="$1"
if [ ! -f "$FILE" ]; then
    echo "Error: $FILE not found"
    exit 1
fi

echo "File size: $(stat -c%s "$FILE") bytes"
```

## Exit Codes

Every command returns a number: 0 = success, non-0 = failure.

```bash
#!/bin/bash

ls /nonexistent
echo "Exit code: $?"    # Prints 2 (file not found)

# Use in script
if cp source.txt dest.txt; then
    echo "Copy succeeded"
else
    echo "Copy failed" >&2
    exit 1
fi
```

## Hands-On: Build a Backup Script

Create `backup.sh`:

```bash
#!/bin/bash

# Usage: ./backup.sh <source_dir> <backup_dir>
set -euo pipefail  # Safety: exit on error, undefined vars, pipe failures

SOURCE="${1:-}"
BACKUP_DIR="${2:-}"
TIMESTAMP=$(date '+%Y%m%d_%H%M%S')
BACKUP_FILE="${BACKUP_DIR}/backup_${TIMESTAMP}.tar.gz"

if [ -z "$SOURCE" ] || [ -z "$BACKUP_DIR" ]; then
    echo "Usage: $0 <source_dir> <backup_dir>"
    exit 1
fi

if [ ! -d "$SOURCE" ]; then
    echo "Error: Source $SOURCE not found"
    exit 1
fi

mkdir -p "$BACKUP_DIR"

echo "Backing up $SOURCE to $BACKUP_FILE ..."
tar -czf "$BACKUP_FILE" "$SOURCE"

if [ $? -eq 0 ]; then
    echo "Backup complete: $BACKUP_FILE"
    echo "Size: $(du -h "$BACKUP_FILE" | cut -f1)"

    # Keep only last 7 backups
    ls -t "${BACKUP_DIR}"/backup_*.tar.gz 2>/dev/null | tail -n +8 | xargs -r rm
    echo "Cleaned up old backups (kept last 7)"
else
    echo "Backup failed" >&2
    exit 1
fi
```

Make it executable and test:

```bash
chmod +x backup.sh
mkdir -p ~/test-source ~/test-backups
echo "important data" > ~/test-source/file.txt
./backup.sh ~/test-source ~/test-backups
ls -la ~/test-backups/
rm -rf ~/test-source ~/test-backups
```

## Self-Quiz

<details>
<summary><strong>1.</strong> What does <code>#!/bin/bash</code> do at the top of a script?</summary>

It's the shebang — tells the system to use `/bin/bash` to interpret the script. Without it, the script might not run correctly.
</details>

<details>
<summary><strong>2.</strong> What's the difference between <code>$FILE</code> and <code>"$FILE"</code>?</summary>

`$FILE` splits the value by spaces (word splitting). `"$FILE"` keeps it as one string. Always quote variables unless you need splitting.
</details>

<details>
<summary><strong>3.</strong> How do you check if a command succeeded in a script?</summary>

Check the exit code with `$?` right after the command, or use `if command; then ...` directly. Exit code 0 = success, non-0 = failure.
</details>

<details>
<summary><strong>4.</strong> What does <code>set -euo pipefail</code> do?</summary>

`-e` exits on any error, `-u` treats undefined variables as errors, `-o pipefail` makes a pipeline fail if any command in it fails. A safety net for scripts.
</details>

## Next Steps

- Customize the backup script for your own use (back up a real directory)
- Try writing a script that monitors disk space and emails you if it's low
- Move to [Chapter 7: Package Management & Services](chapter7.md)

> [!NOTE] Ask followup questions!
> Script not working as expected? Want to automate something specific? Ask your teacher.

---

*Reference: [Linux Command Cheatsheet](reference/0001-command-cheatsheet.md) | [Bash Guide (Greg's Wiki)](https://mywiki.wooledge.org/BashGuide)*
