---
id: a1b2c3d4-1008-4000-8000-000000000008
title: Shell Scripting
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001008
---
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

## Common Patterns

| Task | Bash | PowerShell |
|------|------|------------|
| List files | `ls *.txt` | `Get-ChildItem *.txt` |
| Filter output | `grep pattern` | `Select-String pattern` |
| Parse JSON | `jq .key` | `ConvertFrom-Json` |
| Exit on error | `set -e` | `$ErrorActionPreference = "Stop"` |
| Default variable | `${var:-default}` | `$var ?? "default"` |

## Best Practices

- Always quote variables to prevent word splitting
- Use `set -euo pipefail` in Bash for strict mode
- Prefer `[[ ]]` over `[ ]` in Bash
- Name scripts with `.sh` or `.ps1` extensions
- Add `#!/bin/bash` shebang to Bash scripts
- Validate arguments at the start
- Handle errors with `trap` (Bash) or `try/catch` (PowerShell)

**Links**: [[Command Line Productivity]] | [[Dev Environment Setup]] | [[Git Aliases]] | [[CI CD Pipelines]] | [[Environment Variables]]
