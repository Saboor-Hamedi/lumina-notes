---
id: a1b2c3d4-1142-4000-8000-000000000142
title: Functional Programming Concepts
language: markdown
tags: [web-dev, programming, functional, concepts]
selection: null
isPinned: false
timestamp: 1781500001142
---
# Functional Programming Concepts

Functional programming (FP) is a paradigm that treats computation as the evaluation of mathematical functions, avoiding mutable state and side effects.

## Core Principles

| Principle | Description | Imperative vs FP |
|-----------|-------------|------------------|
| Immutability | Data cannot be changed | `x++` vs `x + 1` |
| Pure Functions | Output depends only on input | Random, I/O are impure |
| Referential Transparency | Expression can be replaced with its value | `2 + 3` ↔ `5` |
| First-Class Functions | Functions are values | Pass, return, compose |
| Higher-Order Functions | Functions that take/return functions | map, filter, reduce |

## Pure Functions

```javascript
// IMPURE — modifies external state + depends on external
let total = 0;
function addToTotal(x) {
    total += x;
    return total;
}

// PURE — same input → same output, no side effects
function add(a, b) {
    return a + b;
}
```

**Benefits**: Testable, memoizable, parallelizable, predictable.

## Immutability

```javascript
// Mutable
const user = { name: "Alice", age: 30 };
user.age = 31;  // Mutation

// Immutable
const user = { name: "Alice", age: 30 };
const updatedUser = { ...user, age: 31 };  // New object

// Immutable array operations
const arr = [1, 2, 3];
const added = [...arr, 4];        // [1, 2, 3, 4]
const removed = arr.filter(x => x !== 2);  // [1, 3]
const updated = arr.map(x => x * 2);       // [2, 4, 6]
```

## Higher-Order Functions

```javascript
// Function that returns a function
function multiplyBy(factor) {
    return (x) => x * factor;
}

const double = multiplyBy(2);
const triple = multiplyBy(3);

double(5)  // 10
triple(5)  // 15

// Function composition
const compose = (f, g) => (x) => f(g(x));
const add1 = (x) => x + 1;
const toString = (x) => `${x}`;
const add1ToString = compose(toString, add1);
add1ToString(5)  // "6"
```

## Currying

```javascript
// Normal
function add(a, b, c) { return a + b + c; }

// Curried
function curriedAdd(a) {
    return (b) => {
        return (c) => a + b + c;
    };
}

curriedAdd(1)(2)(3)  // 6

// Practical: partial application
const fetchData = (baseUrl) => (endpoint) => (params) =>
    fetch(`${baseUrl}/${endpoint}?${new URLSearchParams(params)}`);

const api = fetchData("https://api.example.com");
const getUsers = api("users");
const users = await getUsers({ limit: 10 });
```

## Functors and Monads

```javascript
// Functor — something that can be mapped over
// Array is a functor: [1, 2, 3].map(x => x + 1) → [2, 3, 4]

// Maybe monad — handles null/undefined safely
class Maybe {
    constructor(value) { this._value = value; }
    static of(value) { return new Maybe(value); }
    isNothing() { return this._value === null || this._value === undefined; }

    map(fn) {
        return this.isNothing() ? Maybe.of(null) : Maybe.of(fn(this._value));
    }

    orElse(defaultValue) {
        return this.isNothing() ? defaultValue : this._value;
    }
}

const result = Maybe.of(null)
    .map(x => x.name)        // Won't crash on null
    .map(x => x.toUpperCase())
    .orElse("default");      // → "default"
```

## Function Composition Pipelines

```javascript
const pipeline = (value, ...fns) => fns.reduce((acc, fn) => fn(acc), value);

const result = pipeline(
    { name: "  Alice  ", age: 30 },
    (u) => ({ ...u, name: u.name.trim() }),         // Trim name
    (u) => ({ ...u, ageCategory: u.age >= 18 ? "adult" : "minor" }),  // Categorize
    (u) => `${u.name} (${u.ageCategory})`            // Format output
);
// → "Alice (adult)"
```

## Recursion

```javascript
// Instead of loops, use recursion
function factorial(n) {
    return n <= 1 ? 1 : n * factorial(n - 1);
}

// Tail recursion (optimized by some compilers)
function factorialTail(n, acc = 1) {
    return n <= 1 ? acc : factorialTail(n - 1, n * acc);
}

// Recursive data structures
function deepFreeze(obj) {
    Object.keys(obj).forEach(key => {
        if (typeof obj[key] === "object" && obj[key] !== null) {
            deepFreeze(obj[key]);
        }
    });
    return Object.freeze(obj);
}
```

## Lazy Evaluation

```javascript
// Generator — lazy sequence
function* fibonacci() {
    let a = 0, b = 1;
    while (true) {
        yield a;
        [a, b] = [b, a + b];
    }
}

const fib = fibonacci();
fib.next().value  // 0
fib.next().value  // 1
fib.next().value  // 1
fib.next().value  // 2

// Take first 10 even fibonacci numbers
const result = [];
for (const n of fibonacci()) {
    if (n % 2 === 0) result.push(n);
    if (result.length === 10) break;
}
```

## FP in Practice

| Language | FP Support |
|----------|-----------|
| Haskell | Purely functional |
| Elm | Purely functional (frontend) |
| Clojure | Immutable by default |
| Scala | Hybrid OOP/FP |
| JavaScript | Multi-paradigm, good FP support |
| Python | Multi-paradigm, limited FP |
| Rust | Immutability by default, functional features |
| Java | Modern Java has streams, Optional |

**Links**: [[Design Patterns]] | [[Clean Code Principles]] | [[TypeScript]] | [[JavaScript]] | [[Algorithm Paradigms]]
