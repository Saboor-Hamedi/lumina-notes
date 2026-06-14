---
id: dev-051-0000-0000-0000-000000000035
title: Python Imports and Modules
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781800000013
---
# Python Imports and Modules

**Links**: [[Dev Environment Setup]] | [[Python Virtual Environments]] | [[Unit Testing Guide]] | [[Code Architecture Patterns]] | [[Object-Oriented Programming]]

## Module Structure

```
my_project/
├── main.py
├── utils/
│   ├── __init__.py
│   ├── strings.py
│   └── math.py
└── tests/
    └── test_strings.py
```

## Import Types

```python
import os
from os.path import join
import numpy as np
from collections import defaultdict, Counter
from .strings import capitalize      # Relative
```

## __init__.py Controls Exports

```python
from .strings import capitalize, reverse
__all__ = ["capitalize", "reverse"]
```

## __name__ == "__main__"

```python
def main():
    print("Running directly")

if __name__ == "__main__":
    main()
```

## Common Pitfalls

| Pitfall | Solution |
|---------|----------|
| Circular imports | Restructure, late imports |
| Not found | Check sys.path |
| Relative import outside package | Use absolute imports |
| Side effects at module level | Wrap in main() |

**Next**: [[Object-Oriented Programming]] — OOP fundamentals