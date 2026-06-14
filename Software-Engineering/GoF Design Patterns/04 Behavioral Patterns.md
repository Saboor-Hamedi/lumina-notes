---
tags: [design-patterns, gof, behavioral]
---

# 04 — Behavioral Patterns

## Observer

**Intent**: One-to-many dependency so when one object changes, all dependents are notified.

```python
class Subject:
    def __init__(self):
        self._observers = []
    def attach(self, observer): self._observers.append(observer)
    def notify(self, event, data=None):
        for obs in self._observers:
            obs.update(event, data)

class EmailNotifier(Observer):
    def update(self, event, data):
        if event == "user_registered":
            print(f"Sending welcome to {data['email']}")
```

## Strategy

**Intent**: Define a family of algorithms, encapsulate each, make them interchangeable.

```python
class ShoppingCart:
    def __init__(self, strategy: PaymentStrategy):
        self._strategy = strategy
    def checkout(self, total: float) -> str:
        return self._strategy.pay(total)

cart = ShoppingCart(PayPalPayment("alice@example.com"))
```

## Command

**Intent**: Encapsulate a request as an object, enabling undo/redo, queuing, and logging.

```python
class CommandHistory:
    def execute(self, cmd: Command):
        cmd.execute()
        self._history.append(cmd)
    def undo_last(self):
        self._history.pop().undo()
```

## Iterator

**Intent**: Provide sequential access to elements without exposing underlying representation.

```python
class Range:
    def __iter__(self):
        self._current = self._start
        return self
    def __next__(self):
        if self._current >= self._end: raise StopIteration
        value = self._current; self._current += 1; return value
```

## Remaining Behavioral Patterns

| Pattern | Intent |
|---------|--------|
| **Template Method** | Define skeleton of algorithm, let subclasses fill in steps |
| **State** | Allow object to alter its behavior when its internal state changes |
| **Visitor** | Separate algorithms from the objects they operate on |
| **Mediator** | Reduce chaotic communication between objects via a mediator |
| **Chain of Responsibility** | Pass request along a chain of handlers until one handles it |
| **Interpreter** | Define grammar and interpret sentences in that language |
| **Memento** | Capture and restore object's internal state without violating encapsulation |

**Links**: [[Software-Engineering/GoF Design Patterns/03 Structural Patterns]] | [[Software-Engineering/GoF Design Patterns/05 Modern Alternatives & Anti-Patterns]] | [[Software-Engineering/GoF Design Patterns/02 Creational Patterns]]
**See also**: [[Software Architecture Patterns]], [[Functional Programming Concepts]]
