---
id: a1b2c3d4-1022-4000-8000-000000000022
title: Rust
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001022
---
# Rust

Rust is a systems programming language focused on safety, speed, and concurrency. It guarantees memory safety without a garbage collector.

## Ownership Rules

1. Each value has exactly one owner
2. When the owner goes out of scope, the value is dropped
3. Ownership can be moved or borrowed (but not both simultaneously)

```rust
let s = String::from("hello"); // s owns the string
let t = s;                      // ownership moves to t
// println!("{}", s);           // ❌ s is no longer valid
```

## Borrowing & References

```rust
// Immutable borrow (many allowed)
fn len(s: &String) -> usize {
    s.len()
}

// Mutable borrow (only one at a time)
fn push_world(s: &mut String) {
    s.push_str(" world");
}
```

**Rule**: You may have one mutable reference OR any number of immutable references, but not both simultaneously.

## Lifetime Annotations

```rust
// 'a ties the lifetime of the return to both parameters
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}
```

## Pattern Matching

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}

match result {
    Ok(value) => println!("Success: {}", value),
    Err(e) => eprintln!("Error: {}", e),
}

// Destructuring
let (x, y) = (1, 2);
```

## Error Handling

```rust
// Propagate errors
fn read_file(path: &str) -> Result<String, io::Error> {
    let mut file = File::open(path)?;  // ? propagates error
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;
    Ok(contents)
}
```

## Concurrency

```rust
// Message passing (channels)
let (tx, rx) = mpsc::channel();
thread::spawn(move || {
    tx.send("hello").unwrap();
});

// Shared state (Mutex)
let counter = Arc::new(Mutex::new(0));
let mut handles = vec![];

for _ in 0..10 {
    let counter = Arc::clone(&counter);
    handles.push(thread::spawn(move || {
        let mut num = counter.lock().unwrap();
        *num += 1;
    }));
}
```

## Key Features

| Feature | Benefit |
|---------|---------|
| Zero-cost abstractions | High-level without runtime overhead |
| Fearless concurrency | Compiler prevents data races |
| Pattern matching | Exhaustive checking |
| Trait system | Polymorphism without inheritance |
| Cargo | Build system, package manager, test runner |

## Common Traits

```rust
// Derive macros
#[derive(Debug, Clone, PartialEq)]
struct Point { x: i32, y: i32 }

// Custom trait
trait Speak {
    fn speak(&self) -> String;
}
```

**Links**: [[Memory Management]] | [[Concurrency Models]] | [[WebAssembly]] | [[Package Managers]] | [[Build Tools]]
