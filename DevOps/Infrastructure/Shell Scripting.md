---
id: a1b2c3d4-1008-4000-8000-000000000008
title: Shell Scripting
language: markdown
tags: [devops, infrastructure, shell, scripting]
selection: null
isPinned: false
timestamp: 1781500001008
---

**Links**: [[Dev Environment Setup]] | [[Developer Workflow Automation]] | [[Package Managers]] | [[Ansible]] | [[Terraform]] | [[Vagrant]]


# Shell Scripting

Shell scripting automates repetitive tasks, pipelines, and system administration. This covers both Bash (Linux/macOS) and PowerShell (Windows).

## Bash Basics

### Variables

```bash
NAME="World"
echo "Hello, $NAME"
readonly CONST="fixed"
```

### Control Flow

```bash
if [ -f "$file" ]; then
    echo "File exists"
elif [ -d "$dir" ]; then
    echo "Directory exists"
fi

for i in {1..5}; do
    echo "Count: $i"
done

while read -r line; do
    echo "$line"
done < file.txt
```

### Functions

```bash
greet() {
    local name="$1"
    echo "Hello, $name"
}
greet "Alice"
```

## Error Handling

```bash
#!/bin/bash
set -euo pipefail   # Exit on error, undefined vars, pipe failures

cleanup() {
    echo "Cleaning up..."
    rm -f /tmp/tempfile
}
trap cleanup EXIT       # Always run cleanup on exit
trap 'echo "Error on line $LINENO"' ERR   # Print line number on error

if ! mkdir /data/backup; then
    echo "Failed to create backup directory" >&2
    exit 1
fi
```

Exit codes: `0` = success, `1–127` = user-defined errors, `126` = not executable, `127` = command not found, `128+` = signal-based.

## Argument Parsing with getopts

```bash
#!/bin/bash
usage() { echo "Usage: $0 -f file -o output [-v]"; exit 1; }

while getopts "f:o:v" opt; do
    case "$opt" in
        f) file="$OPTARG" ;;
        o) output="$OPTARG" ;;
        v) verbose=true ;;
        *) usage ;;
    esac
done

echo "File: ${file:-default.txt}"
echo "Output: ${output:-out.log}"
```

## PowerShell Basics

### Variables

```powershell
$name = "World"
Write-Host "Hello, $name"
Set-Variable -Name CONST -Value "fixed" -Option Constant
```

### Control Flow

```powershell
if (Test-Path $file) {
    Write-Host "File exists"
}

foreach ($i in 1..5) {
    Write-Host "Count: $i"
}

Get-Content file.txt | ForEach-Object {
    Write-Host $_
}
```

### Functions

```powershell
function Greet {
    param($name)
    "Hello, $name"
}
Greet "Alice"
```

## Common One-Liner Patterns

| Task | Bash | PowerShell |
|------|------|------------|
| List files | `ls *.txt` | `Get-ChildItem *.txt` |
| Filter output | `grep pattern` | `Select-String pattern` |
| Parse JSON | `jq .key` | `ConvertFrom-Json` |
| Exit on error | `set -e` | `$ErrorActionPreference = "Stop"` |
| Default variable | `${var:-default}` | `$var ?? "default"` |
| Substring | `"${var:0:5}"` | `$var.Substring(0,5)` |
| Replace all | `"${var//old/new}"` | `$var -replace "old","new"` |
| Uppercase | `"${var^^}"` | `$var.ToUpper()` |
| Trim whitespace | `xargs` | `$var.Trim()` |

## Debugging Tips

| Technique | Bash | PowerShell |
|-----------|------|------------|
| Trace execution | `set -x` or `bash -x script.sh` | `Set-PSDebug -Trace 1` |
| Syntax check | `bash -n script.sh` | `Set-PSDebug -Strict` |
| Static analysis | `shellcheck script.sh` | `Invoke-ScriptAnalyzer` |
| Dry run | `set -n` (no execution) | `-WhatIf` parameter |
| Step through | `trap 'read -p "line $LINENO"' DEBUG` | `Set-PSBreakpoint` |

## Best Practices

- Always quote variables to prevent word splitting
- Use `set -euo pipefail` in Bash for strict mode
- Prefer `[[ ]]` over `[ ]` in Bash
- Name scripts with `.sh` or `.ps1` extensions
- Add `#!/bin/bash` shebang to Bash scripts
- Validate arguments at the start
- Handle errors with `trap` (Bash) or `try/catch` (PowerShell)
- Run `shellcheck` on all Bash scripts in CI

**Links**: [[Command Line Productivity]] | [[Dev Environment Setup]] | [[Git Aliases]] | [[CI CD Pipelines]] | [[Environment Variables]]
