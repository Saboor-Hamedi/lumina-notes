---
tags: [design-patterns, gof, modern-alternatives, antipatterns]
---

# 05 — Modern Alternatives & Anti-Patterns

## Modern Alternatives

| Pattern | Modern Replacement |
|---------|-------------------|
| Singleton | Module-level constant, dependency injection |
| Iterator | Generators, built-in `__iter__` protocol |
| Strategy | First-class functions, `functools.partial` |
| Observer | Async event buses, reactive streams |
| Command | Lambda functions, message queues |
| Factory | Direct constructor calls, DI containers |
| Decorator | Python decorators, middleware chains |

## Pattern Selection Decision Tree

```
Pattern needed?
├── Object creation?
│   ├── Single instance → Singleton
│   ├── Subclass decides type → Factory Method
│   ├── Related families → Abstract Factory
│   ├── Complex construction → Builder
│   └── Clone existing → Prototype
├── Object structure?
│   ├── Incompatible interfaces → Adapter
│   ├── Runtime responsibilities → Decorator
│   ├── Control access → Proxy
│   ├── Simplify subsystem → Facade
│   └── Tree structures → Composite
└── Object behavior?
    ├── State changes notify many → Observer
    ├── Swappable algorithms → Strategy
    ├── Undo/redo, queuing → Command
    └── Algorithm skeleton → Template Method
```

## Anti-Patterns & Common Misuses

| Anti-Pattern | Why It's Wrong | Better Approach |
|-------------|---------------|-----------------|
| **Singleton overuse** | Global state, testing issues | DI, module-level instances |
| **Pattern for pattern's sake** | Over-engineering | Simpler solution first |
| **God object** | Single class does everything | Decompose, use Facade |
| **Hard-coded Strategy** | Strategy without variation | Remove pattern, use function |

**Links**: [[Software-Engineering/GoF Design Patterns/01 Classification & Overview]] | [[Software-Engineering/GoF Design Patterns/02 Creational Patterns]] | [[Software-Engineering/GoF Design Patterns/03 Structural Patterns]] | [[Software-Engineering/GoF Design Patterns/04 Behavioral Patterns]]
**See also**: [[Clean Code Principles]], [[Functional Programming Concepts]]
