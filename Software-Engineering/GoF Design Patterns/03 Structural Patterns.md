---
tags: [design-patterns, gof, structural]
---

# 03 — Structural Patterns

## Adapter

**Intent**: Convert one interface to another so incompatible classes can work together.

```python
class PrinterAdapter(ModernLogger):
    def __init__(self, legacy: LegacyPrinter):
        self._legacy = legacy
    def log(self, message: str) -> None:
        self._legacy.print_legacy(message.upper())
```

Use when integrating legacy systems, third-party libraries, or incremental refactoring.

## Decorator

**Intent**: Attach additional responsibilities to an object dynamically.

```python
def log_duration(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        print(f"{func.__name__} took {time.perf_counter() - start:.4f}s")
        return result
    return wrapper
```

Use when responsibilities need runtime add/remove, when subclassing explodes.

## Proxy

**Intent**: Provide a surrogate for another object to control access.

```python
class ImageProxy:
    def __init__(self, filename: str):
        self.filename = filename
        self._real_image = None
    def display(self):
        if self._real_image is None:
            self._real_image = HeavyImage(self.filename)
        self._real_image.display()
```

**Variants**: Virtual (lazy), Protection (access control), Remote, Cache.

## Facade

**Intent**: Provide a unified interface to a set of subsystem interfaces.

```python
class ComputerFacade:
    def start(self):
        self._cpu.freeze()
        data = self._hd.read(0, 1024)
        self._memory.load(0, data)
        self._cpu.jump(0)
        self._cpu.execute()
```

## Remaining Structural Patterns

| Pattern | Intent |
|---------|--------|
| **Composite** | Compose objects into tree structures (part-whole hierarchies) |
| **Bridge** | Decouple abstraction from implementation so both can vary independently |
| **Flyweight** | Share fine-grained objects efficiently (e.g., character glyphs in text editor) |

**Links**: [[Software-Engineering/GoF Design Patterns/02 Creational Patterns]] | [[Software-Engineering/GoF Design Patterns/04 Behavioral Patterns]] | [[Software-Engineering/GoF Design Patterns/05 Modern Alternatives & Anti-Patterns]]
**See also**: [[API Gateway Patterns]], [[Clean Code Principles]]
