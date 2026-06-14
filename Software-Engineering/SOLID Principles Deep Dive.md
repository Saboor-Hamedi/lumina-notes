---
id: a1b2c3d4-1207-4000-8000-000000000207
title: SOLID Principles Deep Dive
language: markdown
tags: [software-engineering]
selection: null
isPinned: false
timestamp: 1781500001207
---
# SOLID Principles Deep Dive

SOLID is a set of five object-oriented design principles for creating maintainable, extensible software.

## Single Responsibility Principle (SRP)

A class should have one, and only one, reason to change.

```python
# BAD: Multiple responsibilities
class Order:
    def calculate_total(self): ...
    def save_to_database(self): ...
    def send_confirmation_email(self): ...
    def generate_invoice_pdf(self): ...

# GOOD: Separate concerns
class Order:
    def calculate_total(self): ...

class OrderRepository:
    def save(self, order): ...

class EmailService:
    def send_confirmation(self, order): ...

class InvoiceGenerator:
    def generate(self, order): ...
```

**Signs of violation**: Class has methods in unrelated domains, class changes frequently for different reasons.

## Open-Closed Principle (OCP)

Software entities should be open for extension but closed for modification.

```python
# BAD: Adding new shape requires modifying AreaCalculator
class AreaCalculator:
    def calculate(self, shapes):
        total = 0
        for shape in shapes:
            if isinstance(shape, Circle):
                total += 3.14 * shape.radius ** 2
            elif isinstance(shape, Rectangle):
                total += shape.width * shape.height
        return total

# GOOD: Polymorphic extension
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self): pass

class Circle(Shape):
    def __init__(self, radius): self.radius = radius
    def area(self): return 3.14 * self.radius ** 2

class Rectangle(Shape):
    def __init__(self, w, h): self.w, self.h = w, h
    def area(self): return self.w * self.h

class Triangle(Shape):          # New shape — NO modification needed
    def __init__(self, b, h): self.b, self.h = b, h
    def area(self): return 0.5 * self.b * self.h

class AreaCalculator:
    def calculate(self, shapes):
        return sum(s.area() for s in shapes)   # Closed for modification
```

## Liskov Substitution Principle (LSP)

Subtypes must be substitutable for their base types.

```python
# BAD: Violates LSP
class Bird:
    def fly(self): return "Flying"

class Penguin(Bird):           # Penguin is a Bird, but...
    def fly(self): raise Exception("Can't fly")  # BREAKS substitution

def let_it_fly(bird: Bird):
    return bird.fly()          # Fails for Penguin!

# GOOD: Redesign hierarchy
class Bird(ABC):
    @abstractmethod
    def move(self): pass

class FlyingBird(Bird):
    def move(self): return "Flying"

class Penguin(Bird):
    def move(self): return "Swimming"

# All subtypes are safely substitutable
```

**Behavioral contracts LSP requires**:
- Same preconditions or weaker
- Same postconditions or stronger
- Invariants preserved
- Exceptions: subtypes can throw fewer/same types, not more

## Interface Segregation Principle (ISP)

Clients should not be forced to depend on interfaces they don't use.

```python
# BAD: Fat interface
class WorkerInterface:
    def work(self): pass
    def eat(self): pass
    def sleep(self): pass

class Human(WorkerInterface):        # Uses all three
    def work(self): ...
    def eat(self): ...
    def sleep(self): ...

class Robot(WorkerInterface):        # Only works — forced to implement eat/sleep
    def work(self): ...
    def eat(self): raise NotImplementedError  # Unused!
    def sleep(self): raise NotImplementedError

# GOOD: Segregated interfaces
class Workable(ABC):
    @abstractmethod
    def work(self): pass

class Eatable(ABC):
    @abstractmethod
    def eat(self): pass

class Human(Workable, Eatable):      # Implements only what it needs
    def work(self): ...
    def eat(self): ...

class Robot(Workable):               # Clean — only work
    def work(self): ...
```

**Signs of violation**: Classes have empty or throwing implementations, clients depend on methods they never call.

## Dependency Inversion Principle (DIP)

High-level modules should not depend on low-level modules. Both should depend on abstractions.

```python
# BAD: High-level depends on low-level
class MySQLDatabase:
    def save_user(self, user):
        # Direct MySQL query
        pass

class UserService:
    def __init__(self):
        self.db = MySQLDatabase()       # Tight coupling to MySQL

    def create_user(self, name, email):
        user = User(name, email)
        self.db.save_user(user)

# GOOD: Both depend on abstraction
from abc import ABC, abstractmethod

class UserRepository(ABC):              # Abstraction
    @abstractmethod
    def save(self, user): pass

class MySQLRepository(UserRepository):  # Low-level
    def save(self, user):
        # MySQL-specific code
        pass

class PostgresRepository(UserRepository):  # Another implementation
    def save(self, user):
        # PostgreSQL-specific code
        pass

class UserService:                       # High-level
    def __init__(self, repo: UserRepository):  # Depends on abstraction
        self.repo = repo

    def create_user(self, name, email):
        user = User(name, email)
        self.repo.save(user)

# Wiring (done at composition root)
repo = MySQLRepository()
service = UserService(repo)
```

## SOLID Benefits

| Principle | Benefit | Violation Cost |
|-----------|---------|---------------|
| SRP | Focused, testable classes | God classes, fragile code |
| OCP | Easy to extend without risk | Switch statements everywhere |
| LSP | Safe polymorphism | Runtime surprises, fragile inheritance |
| ISP | Minimal dependencies | Bloated interfaces, coupling |
| DIP | Decoupled, testable architecture | Rigid, hard-to-test code |

## Real-World Indicators

```python
# SRP violation: God class with 2000+ lines
# OCP violation: 15 case/if-else statements checking type
# LSP violation: isinstance checks before calling methods
# ISP violation: Python ABC with NotImplementedError methods
# DIP violation: Hardcoded new() calls to concrete classes
```

**Links**: [[Agile Development]] | [[Clean Code Principles]] | [[Code Review Best Practices]] | [[Code Review Process]] | [[Coding Interview Patterns]] | [[Debugging Strategies]] | [[Developer Experience]] | [[Environment Variables]] | [[Error Handling Patterns]] | [[Estimation and Planning]] | [[Feature Flags and Toggles]] | [[GoF Design Patterns]] | [[Incident Response]] | [[Internationalization]] | [[Monorepo vs Polyrepo]] | [[Monorepo with Nx and Turborepo]] | [[Onboarding and Mentoring]] | [[Open Source]] | [[Performance Profiling]] | [[Programming Resources]] | [[Refactoring Techniques]] | [[Regular Expressions]] | [[Scrum Framework]] | [[Software Design Principles]] | [[System Design Interview]] | [[Technical Debt Management]] | [[Technical Writing]] | [[Unicode and Encoding]] | [[Vim and Neovim]] | [[Virtualization]]
