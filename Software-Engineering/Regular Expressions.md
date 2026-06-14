---
id: a1b2c3d4-0008-4000-8000-000000000008
title: Regular Expressions
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781400000008
---
# Regular Expressions

Regular expressions (regex) are patterns used to match character combinations in strings.

## Common Patterns

| Pattern | Matches |
|---------|---------|
| `.` | Any character except newline |
| `\d` | Digit (0-9) |
| `\w` | Word character (a-z, A-Z, 0-9, _) |
| `\s` | Whitespace |
| `^` | Start of string |
| `$` | End of string |
| `*` | Zero or more |
| `+` | One or more |
| `?` | Zero or one |

## Practical Examples

```python
import re

# Email validation
pattern = r'^[\w\.-]+@[\w\.-]+\.\w+$'
re.match(pattern, "user@example.com")

# Extract all URLs
re.findall(r'https?://\S+', text)

# Replace whitespace
re.sub(r'\s+', '_', "hello world")  # "hello_world"
```

## Character Classes

```regex
[aeiou]     # Any vowel
[^0-9]      # Not a digit
[a-zA-Z]    # Any letter
(?:cat|dog) # Non-capturing group
(?P<name>\w+)  # Named group
```

**See also**: [[NLP Pipeline Design]], [[Functional Programming]], [[Dev Environment Setup]]