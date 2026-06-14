---
tags: [design-patterns, gof, creational]
---

# 02 — Creational Patterns

## Singleton

**Intent**: Ensure a class has only one instance and provide a global point of access.

```python
import threading

class SingletonMeta(type):
    _instances = {}
    _lock = threading.Lock()
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            with cls._lock:
                if cls not in cls._instances:
                    instance = super().__call__(*args, **kwargs)
                    cls._instances[cls] = instance
        return cls._instances[cls]

class Logger(metaclass=SingletonMeta):
    def log(self, msg: str) -> None:
        print(f"[LOG] {msg}")
```

**When to use**: Shared resources (DB pools, thread pools), configuration objects, logging.

**When NOT**: Testing difficulty, hidden global state, SRP violation. In Python, module-level singletons are simpler.

## Factory Method

**Intent**: Define interface for creating an object, let subclasses decide which class to instantiate.

```python
class Transport(ABC):
    @abstractmethod
    def deliver(self) -> str: ...

class Truck(Transport):
    def deliver(self) -> str: return "Delivering by land"

class Logistics(ABC):
    @abstractmethod
    def create_transport(self) -> Transport: ...
    def plan_delivery(self) -> str:
        return self.create_transport().deliver()

class RoadLogistics(Logistics):
    def create_transport(self) -> Transport: return Truck()
```

## Abstract Factory

**Intent**: Create families of related objects without specifying concrete classes.

| Aspect | Factory Method | Abstract Factory |
|--------|---------------|------------------|
| Scope | Single product | Product families |
| Complexity | Low | Medium |
| Flexibility | Subclass decides | Entire family swapped |

## Builder

**Intent**: Separate construction of complex object from its representation.

```python
query = (SQLQueryBuilder()
    .select("id, name")
    .from_table("users")
    .where("active = 1")
    .order_by("name")
    .limit(10)
    .build())
```

## Prototype

**Intent**: Create new objects by copying a prototype instance.

```python
original = Document("Hello", {"author": "Alice"}, ["draft"])
shallow = copy.copy(original)     # Shared nested refs
deep = copy.deepcopy(original)    # Fully independent
```

**Links**: [[Software-Engineering/GoF Design Patterns/03 Structural Patterns]] | [[Software-Engineering/GoF Design Patterns/04 Behavioral Patterns]] | [[Software-Engineering/GoF Design Patterns/05 Modern Alternatives & Anti-Patterns]]
**See also**: [[Software Architecture Patterns]], [[Functional Programming Concepts]]
