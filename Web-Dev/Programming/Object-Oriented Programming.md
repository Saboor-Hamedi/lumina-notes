---
id: dev-050-0000-0000-0000-000000000034
title: Object-Oriented Programming
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781800000012
---
# Object-Oriented Programming

**Links**: [[Programming Language Paradigms]] | [[Software Design Principles]] | [[GoF Design Patterns]] | [[Code Architecture Patterns]] | [[Error Handling Patterns]]

## Four Pillars

| Pillar | Description |
|--------|-------------|
| **Encapsulation** | Bundle data + methods, hide internal state |
| **Inheritance** | Child class derives from parent |
| **Polymorphism** | Same interface, different implementations |
| **Abstraction** | Hide complexity, expose essentials |

## Encapsulation

```python
class BankAccount:
    def __init__(self, owner: str, balance: float = 0):
        self.owner = owner
        self.__balance = balance

    def deposit(self, amount: float):
        if amount <= 0:
            raise ValueError("Must be positive")
        self.__balance += amount

    @property
    def balance(self) -> float:
        return self.__balance
```

## Polymorphism

```python
class Animal:
    def speak(self) -> str:
        raise NotImplementedError

class Dog(Animal):
    def speak(self) -> str:
        return "Woof!"

class Cat(Animal):
    def speak(self) -> str:
        return "Meow!"
```

## Composition over Inheritance

```python
class Engine:
    def start(self): print("Engine started")

class Car:
    def __init__(self):
        self.engine = Engine()

    def start(self):
        self.engine.start()
        print("Car ready")
```

**Next**: [[GoF Design Patterns]] — Common design patterns