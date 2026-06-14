---
id: a1b2c3d4-1150-4000-8000-000000000150
title: Rust Ownership and Borrowing
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001150
---
# Rust Ownership and Borrowing

Rust's ownership system guarantees memory safety without a garbage collector. It's the language's most distinctive feature.

## Ownership Rules

1. Each value has exactly **one owner**
2. When the owner goes out of scope, the value is **dropped**
3. Ownership can be **transferred** (moved)

```rust
fn main() {
    let s1 = String::from("hello"); // s1 owns the String
    let s2 = s1;                    // ownership MOVED to s2
    // println!("{}", s1);          // ERROR: s1 no longer valid
    println!("{}", s2);             // OK: s2 owns it
}
```

## Copy vs Move Types

| Type | Trait | Behavior |
|------|-------|----------|
| Integers | `Copy` | Implicitly copied on assignment |
| Floats | `Copy` | Implicitly copied |
| Booleans | `Copy` | Implicitly copied |
| Characters | `Copy` | Implicitly copied |
| Tuples of Copy types | `Copy` | Implicitly copied |
| Strings | `!Copy` (Clone) | Moved on assignment |
| Vectors | `!Copy` (Clone) | Moved on assignment |
| Structs | `!Copy` (by default) | Moved on assignment |

```rust
// Copy types
let x = 5;
let y = x;      // Copy (both are valid)
println!("{}", x); // OK

// Move types
let s = String::from("hello");
let t = s;       // Move
// println!("{}", s); // ERROR

// Explicit clone
let s = String::from("hello");
let t = s.clone(); // Deep copy
println!("{}", s); // OK (s still owns its data)
```

## Borrowing

Borrowing lets you reference a value without taking ownership.

```rust
fn calculate_length(s: &String) -> usize {  // Borrows, doesn't own
    s.len()
}                                           // s goes out of scope, but nothing is dropped

fn main() {
    let s = String::from("hello");
    let len = calculate_length(&s);         // Pass reference
    println!("{} length: {}", s, len);      // s still valid
}
```

## Mutable References

```rust
fn change(s: &mut String) {
    s.push_str(" world");
}

fn main() {
    let mut s = String::from("hello");
    change(&mut s);
    println!("{}", s);  // "hello world"
}
```

## Borrowing Rules

**At any given time, you can have EITHER:**
- One mutable reference
- Any number of immutable references

```rust
fn main() {
    let mut s = String::from("hello");

    let r1 = &s;       // OK
    let r2 = &s;       // OK
    // let r3 = &mut s; // ERROR: cannot borrow as mutable (already borrowed immutable)
    println!("{} {}", r1, r2);

    let r3 = &mut s;   // OK (immutable refs no longer used)
    r3.push_str("!");
}
```

## Dangling References

Rust prevents dangling references at compile time:

```rust
fn dangle() -> &String {            // ERROR: missing lifetime specifier
    let s = String::from("hello");
    &s                              // s goes out of scope here
}                                   // reference would be dangling

fn no_dangle() -> String {
    let s = String::from("hello");
    s                               // Ownership moved out
}
```

## The Slice Type

Slices are references to a contiguous sequence of elements:

```rust
fn first_word(s: &str) -> &str {
    let bytes = s.as_bytes();
    for (i, &byte) in bytes.iter().enumerate() {
        if byte == b' ' {
            return &s[..i];
        }
    }
    &s[..]
}

fn main() {
    let s = String::from("hello world");
    let word = first_word(&s[..]);

    // s.clear();                    // ERROR: immutable borrow in use
    println!("first word: {}", word);
}
```

## Lifetime Annotations

Lifetimes ensure references are valid as long as they're used:

```rust
// 'a is a lifetime parameter — output lives as long as both inputs
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

fn main() {
    let s1 = String::from("long");
    let result;
    {
        let s2 = String::from("short");
        result = longest(&s1, &s2);  // result's lifetime = shorter of s1, s2
    }
    // println!("{}", result);       // ERROR: s2 dropped, result invalid
}
```

### Lifetime Elision Rules

The compiler infers lifetimes in most cases:

```rust
// No annotation needed:
fn first(x: &str, y: &str) -> &str  // ERROR: needs annotation

fn first<'a>(x: &'a str, _y: &str) -> &'a str { x }  // OK
```

## Lifetime in Structs

```rust
struct Excerpt<'a> {
    part: &'a str,   // Must not outlive the referenced string
}

fn main() {
    let novel = String::from("Call me Ishmael...");
    let first_sentence = novel.split('.').next().unwrap();
    let excerpt = Excerpt { part: first_sentence };
    // excerpt cannot outlive novel
}
```

## Common Patterns

```rust
// Returning a reference to a parameter
fn get_prefix(s: &str) -> &str {
    &s[..3]
}

// Mutable method
impl Counter {
    fn increment(&mut self) {
        self.count += 1;
    }

    fn read(&self) -> u64 {
        self.count
    }
}

// Self-referential struct (needs Pin)
struct SelfRef {
    value: String,
    pointer: *const String,  // Raw pointer, not a reference
}
```

**Links**: [[Rust]] | [[Go Programming]] | [[Memory Management]] | [[Systems Programming]] | [[Operating Systems]]
