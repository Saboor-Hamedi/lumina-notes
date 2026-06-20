# Lesson 2: File I/O & Error Handling

**Lesson 2** · ~35 min · Hands-on

> [!NOTE] Mission tie-in
> Real scripts read and write files — configs, logs, data exports. Error handling is what keeps them from crashing. Together they make your Python scripts production-ready.

## What You'll Learn

- Open, read, and write files
- Use context managers (`with` statements)
- Handle errors with `try`/`except`/`else`/`finally`
- Work with file paths using `pathlib`
- Read and write structured data (JSON, CSV)

## Reading Files

The built-in `open()` function is the foundation. Always pair it with a **context manager** (`with`) so the file is automatically closed.

```python
# Read entire file as one string
with open("hello.txt", "r") as f:
    content = f.read()
    print(content)

# Read line by line (memory-efficient for large files)
with open("hello.txt", "r") as f:
    for line in f:
        print(line.rstrip())  # rstrip removes trailing newline

# Read all lines into a list
with open("hello.txt", "r") as f:
    lines = f.readlines()  # ["line1\n", "line2\n", ...]
```

> [!TIP] The `"r"` mode is the default — you can omit it: `open("hello.txt")`.

## Writing Files

```python
# Write (overwrites existing content)
with open("output.txt", "w") as f:
    f.write("First line\n")
    f.write("Second line\n")

# Append (adds to end of existing file)
with open("output.txt", "a") as f:
    f.write("Third line\n")

# Write multiple lines at once
lines = ["apple\n", "banana\n", "cherry\n"]
with open("fruits.txt", "w") as f:
    f.writelines(lines)
```

| Mode | Behaviour |
|------|-----------|
| `"r"` | Read (default). Error if file doesn't exist. |
| `"w"` | Write. Creates or **overwrites**. |
| `"a"` | Append. Creates if missing. |
| `"r+"` | Read and write. Error if missing. |
| `"x"` | Exclusive creation. Error if exists. |

## Error Handling Basics

```python
try:
    with open("missing.txt", "r") as f:
        content = f.read()
except FileNotFoundError:
    print("That file doesn't exist.")
except PermissionError:
    print("You don't have permission to read that file.")
```

```python
try:
    num = int(input("Enter a number: "))
    result = 10 / num
except ValueError:
    print("That's not a valid number.")
except ZeroDivisionError:
    print("Can't divide by zero.")
else:
    # Runs only if no exception occurred
    print(f"Result: {result}")
finally:
    # Always runs — great for cleanup
    print("Done.")
```

### Common Exception Types

| Exception | When it fires |
|-----------|---------------|
| `FileNotFoundError` | File doesn't exist |
| `PermissionError` | No read/write access |
| `ValueError` | Wrong value for a function |
| `TypeError` | Wrong type for an operation |
| `ZeroDivisionError` | Division by zero |
| `KeyError` | Missing dictionary key |
| `IndexError` | List index out of range |

> [!WARNING] Don't catch **all** exceptions with bare `except:` — you'll hide bugs. Always specify the exception type.

## Raising Exceptions

Raise your own errors when something is invalid:

```python
def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b

# You can also re-raise after logging
try:
    result = divide(10, 0)
except ValueError as e:
    print(f"Caught: {e}")
    raise  # re-raises the same exception
```

## Working with Paths — `pathlib`

`pathlib` is the modern way to handle file paths (replaces `os.path`):

```python
from pathlib import Path

# Define a path
data_dir = Path("data")
log_file = data_dir / "app.log"  # uses / to join paths — clean!

# Create directories
data_dir.mkdir(exist_ok=True)

# Check existence
print(log_file.exists())      # True / False
print(log_file.is_file())     # True / False
print(log_file.suffix)        # ".log"
print(log_file.stem)          # "app"
print(log_file.name)          # "app.log"

# List files in a directory
for p in Path(".").iterdir():
    print(p.name)

# Glob patterns
for py_file in Path(".").glob("**/*.py"):
    print(py_file)

# Read/write shortcuts
content = Path("hello.txt").read_text()          # whole file as string
Path("out.txt").write_text("hello world\n")      # write string
```

> [!TIP] `pathlib` is in the standard library — no pip install needed. Use it whenever you touch file paths.

## Reading & Writing Structured Data

### JSON

```python
import json

data = {
    "name": "Alice",
    "age": 30,
    "skills": ["Python", "SQL"]
}

# Write to file
with open("data.json", "w") as f:
    json.dump(data, f, indent=2)

# Read from file
with open("data.json", "r") as f:
    loaded = json.load(f)
    print(loaded["name"])

# In-memory
json_string = json.dumps(data, indent=2)
parsed = json.loads(json_string)
```

### CSV

```python
import csv

# Reading
with open("users.csv", "r") as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row["name"], row["email"])

# Writing
with open("users.csv", "w", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=["name", "email"])
    writer.writeheader()
    writer.writerow({"name": "Alice", "email": "alice@example.com"})
    writer.writerow({"name": "Bob", "email": "bob@example.com"})
```

> [!TIP] Always pass `newline=""` when writing CSV on Python 3 — avoids extra blank lines on Windows.

## Hands-On: Log Analyzer

Create `log_analyzer.py` that reads a server log file and generates a summary report.

Start by creating a sample log file to work with:

```bash
# On Windows (PowerShell)
@"
[INFO] 2026-06-19 10:00:00 Server started
[ERROR] 2026-06-19 10:01:23 Database connection failed
[INFO] 2026-06-19 10:02:00 Retry attempt 1
[ERROR] 2026-06-19 10:02:30 Database connection failed
[WARN] 2026-06-19 10:03:00 Disk space at 85%
[INFO] 2026-06-19 10:04:00 Request processed in 230ms
[ERROR] 2026-06-19 10:05:00 Timeout on upstream API
[INFO] 2026-06-19 10:06:00 Server shutting down
"@ | Out-File -Encoding utf8 server.log
```

Now write the analyzer:

```python
#!/usr/bin/env python3

import sys
from pathlib import Path
from collections import Counter


def analyze_log(log_path: Path) -> dict:
    """Parse a log file and return summary statistics."""
    if not log_path.exists():
        raise FileNotFoundError(f"Log file not found: {log_path}")

    levels = Counter()
    error_messages = []

    with open(log_path, "r") as f:
        for line in f:
            line = line.strip()
            if not line:
                continue

            # Parse log level from pattern: [LEVEL]
            if line.startswith("["):
                level_end = line.index("]")
                level = line[1:level_end]
                levels[level] += 1

                if level == "ERROR":
                    # Extract message after the timestamp
                    parts = line.split(" ", 3)
                    if len(parts) >= 4:
                        error_messages.append(parts[3])

    return {
        "total_lines": sum(levels.values()),
        "by_level": dict(levels),
        "error_count": levels.get("ERROR", 0),
        "error_messages": error_messages,
    }


def generate_report(stats: dict, output_path: Path):
    """Write a summary report to a file."""
    with open(output_path, "w") as f:
        f.write("=== Log Analysis Report ===\n\n")
        f.write(f"Total log lines: {stats['total_lines']}\n\n")

        f.write("Breakdown by level:\n")
        for level, count in sorted(stats["by_level"].items()):
            bar = "█" * count
            f.write(f"  {level:5s}: {count:3d}  {bar}\n")

        f.write(f"\nErrors found: {stats['error_count']}\n")
        if stats["error_messages"]:
            f.write("\nError details:\n")
            for i, msg in enumerate(stats["error_messages"], 1):
                f.write(f"  {i}. {msg}\n")


if __name__ == "__main__":
    try:
        log_path = Path(sys.argv[1]) if len(sys.argv) > 1 else Path("server.log")
        report_path = Path("report.txt")

        stats = analyze_log(log_path)
        generate_report(stats, report_path)
        print(f"Report written to {report_path}")
        print(f"Found {stats['error_count']} error(s)")

    except FileNotFoundError as e:
        print(f"Error: {e}", file=sys.stderr)
        sys.exit(1)
    except Exception as e:
        print(f"Unexpected error: {e}", file=sys.stderr)
        sys.exit(1)
```

Run it:

```bash
python log_analyzer.py
```

Check `report.txt` for the output. Try running it with a missing file to see error handling in action:

```bash
python log_analyzer.py nonexistent.log
```

### Challenge

1. Add a `--json` flag that writes the report as JSON instead of plain text
2. Add a `--warn-threshold` option that exits with code 1 if warnings exceed a count
3. Support reading gzipped log files (hint: `import gzip`)

## Self-Quiz

<details>
<summary><strong>1.</strong> What does the <code>with</code> statement do when opening a file?</summary>

It's a context manager that ensures the file is automatically closed, even if an exception occurs. No need to call `.close()` manually.
</details>

<details>
<summary><strong>2.</strong> What's the difference between <code>"w"</code> and <code>"a"</code> file modes?</summary>

`"w"` opens for writing and **overwrites** the file if it exists. `"a"` opens for writing and **appends** to the end, preserving existing content.
</details>

<details>
<summary><strong>3.</strong> Why should you avoid bare <code>except:</code>?</summary>

A bare `except:` catches absolutely everything — `KeyboardInterrupt`, `SystemExit`, `MemoryError` — making debugging impossible and potentially hanging the program. Always catch specific exception types.
</details>

<details>
<summary><strong>4.</strong> What does <code>pathlib.Path("data") / "log.txt"</code> do?</summary>

Constructs the path `data/log.txt` (or `data\log.txt` on Windows) using the OS-specific separator. Much cleaner than `os.path.join("data", "log.txt")`.
</details>

## Next Steps

- Add a `--csv` output option to the log analyzer
- Try the [`pathlib` tutorial](https://docs.python.org/3/library/pathlib.html)
- Move to Lesson 3: Modules, Packages & Virtual Environments (coming next)

> [!NOTE] Ask followup questions!
> Code not running? Confused about a concept? Ask your teacher.

---

*Reference: [Python File I/O Docs](https://docs.python.org/3/tutorial/inputoutput.html), [Python Errors Docs](https://docs.python.org/3/tutorial/errors.html)*
