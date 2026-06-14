---
id: a1b2c3d4-1149-4000-8000-000000000149
title: Python Deep Dive
language: markdown
tags: [web-dev, programming, python]
selection: null
isPinned: false
timestamp: 1781500001149
---
# Python Deep Dive

Advanced Python features including decorators, context managers, metaclasses, descriptors, and concurrency patterns.

## Decorators

```python
from functools import wraps
import time

# Basic decorator
def log_calls(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print(f"{func.__name__} returned {result}")
        return result
    return wrapper

@log_calls
def add(a, b):
    return a + b

# Decorator with arguments
def retry(max_attempts=3, delay=1):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts - 1:
                        raise
                    time.sleep(delay)
            return None
        return wrapper
    return decorator

@retry(max_attempts=5, delay=2)
def fetch_data(url):
    return requests.get(url)

# Class-based decorator
class CountCalls:
    def __init__(self, func):
        self.func = func
        self.count = 0

    def __call__(self, *args, **kwargs):
        self.count += 1
        print(f"Call #{self.count} of {self.func.__name__}")
        return self.func(*args, **kwargs)
```

## Context Managers

```python
from contextlib import contextmanager

# Class-based
class DatabaseConnection:
    def __enter__(self):
        self.conn = psycopg2.connect("dbname=test")
        return self.conn

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type:
            self.conn.rollback()
        else:
            self.conn.commit()
        self.conn.close()

with DatabaseConnection() as conn:
    conn.execute("INSERT INTO users ...")

# Generator-based
@contextmanager
def timer(label: str):
    start = time.perf_counter()
    try:
        yield
    finally:
        elapsed = time.perf_counter() - start
        print(f"{label}: {elapsed:.3f}s")

with timer("database query"):
    process_large_query()

# Context manager for temporarily changing state
@contextmanager
def chdir(path):
    old = os.getcwd()
    os.chdir(path)
    try:
        yield
    finally:
        os.chdir(old)
```

## Metaclasses

```python
# Singleton metaclass
class SingletonMeta(type):
    _instances = {}

    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super().__call__(*args, **kwargs)
        return cls._instances[cls]

class Database(metaclass=SingletonMeta):
    def __init__(self):
        self.connection = None

# Registry pattern
class RegistryMeta(type):
    registry = {}

    def __new__(mcs, name, bases, namespace):
        cls = super().__new__(mcs, name, bases, namespace)
        if name != "BaseModel":
            mcs.registry[name] = cls
        return cls

class BaseModel(metaclass=RegistryMeta):
    pass

class User(BaseModel): pass
class Order(BaseModel): pass

print(RegistryMeta.registry)  # {"User": User, "Order": Order}
```

## Descriptors

```python
class ValidatedAttribute:
    def __init__(self, validator):
        self.validator = validator
        self.data = {}

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        return self.data.get(id(obj))

    def __set__(self, obj, value):
        if not self.validator(value):
            raise ValueError(f"Invalid value: {value}")
        self.data[id(obj)] = value

    def __delete__(self, obj):
        del self.data[id(obj)]

class User:
    email = ValidatedAttribute(lambda v: "@" in v)
    age = ValidatedAttribute(lambda v: 0 <= v <= 150)

u = User()
u.email = "alice@example.com"  # OK
u.email = "invalid"            # ValueError
```

## Concurrency Patterns

```python
import asyncio
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor

# Async context manager
class AsyncDatabase:
    async def __aenter__(self):
        self.conn = await create_connection()
        return self.conn

    async def __aexit__(self, *args):
        await self.conn.close()

async def main():
    async with AsyncDatabase() as db:
        result = await db.query("SELECT * FROM users")

# Thread pool for blocking I/O
def process_user(user_id):
    user = fetch_from_db(user_id)
    return transform(user)

with ThreadPoolExecutor(max_workers=10) as executor:
    results = list(executor.map(process_user, user_ids))

# Process pool for CPU-bound work
def compute_prime(n):
    return is_prime(n)

with ProcessPoolExecutor(max_workers=4) as executor:
    results = list(executor.map(compute_prime, numbers))
```

## __slots__ for Memory Optimization

```python
# Without __slots__: each instance has __dict__
class User:
    __slots__ = ("name", "email", "age")

    def __init__(self, name, email, age):
        self.name = name
        self.email = email
        self.age = age

# Memory: ~56 bytes/instance vs ~168 bytes/instance
```

## Property Pattern

```python
class Temperature:
    def __init__(self, celsius=0):
        self._celsius = celsius

    @property
    def celsius(self):
        return self._celsius

    @celsius.setter
    def celsius(self, value):
        if value < -273.15:
            raise ValueError("Below absolute zero")
        self._celsius = value

    @property
    def fahrenheit(self):
        return self._celsius * 9/5 + 32

    @fahrenheit.setter
    def fahrenheit(self, value):
        self._celsius = (value - 32) * 5/9
```

**Links**: [[Type Hints in Python]] | [[Async Python]] | [[Design Patterns]] | [[Clean Code Principles]] | [[Functional Programming Concepts]]
