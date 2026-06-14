---
id: a1b2c3d4-1061-4000-8000-000000000061
title: Java
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001061
---
# Java

Java is a statically-typed, object-oriented language designed for portability (Write Once, Run Anywhere) via the Java Virtual Machine (JVM).

## Key Features

| Feature | Description |
|---------|-------------|
| JVM | Bytecode runs on any JVM implementation |
| Garbage collected | Automatic memory management |
| Strong typing | Types checked at compile time |
| Multi-threaded | Built-in thread support |
| Rich ecosystem | Maven, Spring, Hibernate, billions of devices |

## JVM Architecture

```
Source (.java) → Compiler (javac) → Bytecode (.class) → JVM → Native
```

```
┌────────────────────────────────┐
│         Class Loader            │
├────────────────────────────────┤
│   Runtime Data Areas           │
│  ┌──────────┐  ┌────────────┐ │
│  │   Heap   │  │   Stack    │ │
│  │          │  │ (per thread)│ │
│  ├──────────┤  ├────────────┤ │
│  │ Metaspace│  │ PC Register│ │
│  └──────────┘  └────────────┘ │
├────────────────────────────────┤
│  Execution Engine              │
│  (Interpreter + JIT)           │
└────────────────────────────────┘
```

## Core Language

```java
// Records (Java 16+)
public record User(String name, int age) {}

// Sealed classes (Java 17+)
public sealed class Shape permits Circle, Rectangle {}
public final class Circle extends Shape {}
public final class Rectangle extends Shape {}

// Pattern matching (Java 21+)
if (obj instanceof String s) {
    System.out.println(s.length());
}

// Virtual threads (Java 21+)
Thread.ofVirtual().start(() -> {
    System.out.println("Lightweight thread!");
});
```

## Key Differences from C++

| Feature | Java | C++ |
|---------|------|-----|
| Memory | GC | Manual (RAII) |
| Multiple inheritance | Interfaces only | Multiple classes |
| Pointers | No (references only) | Yes |
| Operators | No overloading | Overloadable |
| Platform | JVM bytecode | Native binary |

## Build Tools

| Tool | Config | Use Case |
|------|--------|----------|
| Maven | pom.xml (XML) | Standard, dependency management |
| Gradle | build.gradle (Groovy/Kotlin) | Fast, flexible, Android |
| Ant | build.xml (XML) | Legacy, simple projects |

## Popular Frameworks

| Framework | Purpose |
|-----------|---------|
| Spring Boot | Microservices, web apps |
| Hibernate | ORM (Object-Relational Mapping) |
| Jakarta EE | Enterprise Java |
| Quarkus | Cloud-native, fast startup |
| Micronaut | Lightweight, compile-time DI |
| JUnit | Unit testing |
| Mockito | Mocking framework |

## Concurrency

```java
// Virtual threads (Java 21+)
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    executor.submit(() -> process(task));
}

// Structured concurrency (Java 21+)
try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
    Future<String> user = scope.fork(() -> fetchUser());
    Future<Integer> orders = scope.fork(() -> fetchOrders());
    scope.join();
    return new Result(user.resultNow(), orders.resultNow());
}
```

**Links**: [[Object-Oriented Programming]] | [[GoF Design Patterns]] | [[Package Managers]] | [[Build Tools]] | [[Concurrency Models]]
