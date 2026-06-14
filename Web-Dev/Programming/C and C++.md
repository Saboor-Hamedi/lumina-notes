---
id: a1b2c3d4-1062-4000-8000-000000000062
title: C and C++
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001062
---
# C and C++

C is a procedural systems language. C++ adds OOP, templates, and modern abstractions on top of C. Both give fine-grained control over memory and hardware.

## C vs C++

| Aspect | C | C++ |
|--------|---|-----|
| Paradigm | Procedural | Multi-paradigm (OOP, generic, functional) |
| Memory | Manual (malloc/free) | Manual + RAII (smart pointers) |
| Abstractions | Structs, functions | Classes, templates, exceptions |
| Standard | C11, C17, C23 | C++20, C++23 |
| Use case | OS kernels, embedded | Games, trading, high-performance apps |

## Memory Safety (Why Rust Exists)

| Bug | C Example | C++ Alternative |
|-----|-----------|-----------------|
| Use-after-free | `free(p); *p = 5;` | `unique_ptr` (freed automatically) |
| Buffer overflow | `char buf[10]; strcpy(buf, long_str);` | `std::string`, `std::vector` |
| Double free | `free(p); free(p);` | RAII, no manual free |
| Null dereference | `int* p = NULL; *p = 5;` | `std::optional`, references |

## Modern C++ (C++20/23)

```cpp
// Concepts (C++20)
template<typename T>
concept Numeric = std::is_arithmetic_v<T>;

auto add(Numeric auto a, Numeric auto b) { return a + b; }

// Ranges (C++20)
std::vector<int> v = {1, 2, 3, 4, 5};
auto result = v | std::views::filter([](int n) { return n % 2 == 0; })
                | std::views::transform([](int n) { return n * n; });

// Smart pointers
auto ptr = std::make_unique<MyClass>(args);

// std::optional (C++17)
std::optional<int> maybe_parse(const std::string& s);
```

## Compilation Model

```
Source (.c/.cpp) → Preprocessor → Compiler → Assembler → Object (.o/.obj)
                                                            ↓
                                           Linker ← Libraries (.a/.so/.lib/.dll)
                                                            ↓
                                                      Executable
```

## Key Libraries

| Library | Purpose |
|---------|---------|
| STL (Standard Template Library) | Containers, algorithms, iterators |
| Boost | Peer-reviewed, portable libraries |
| Qt | Cross-platform GUI |
| fmt | Modern string formatting |
| nlohmann/json | JSON parsing |
| Catch2, Google Test | Unit testing |
| CUDA | GPU programming (NVIDIA) |

## When to Use C/C++

| Use Case | Language |
|----------|----------|
| Operating systems | C (Linux kernel) |
| Embedded systems | C (microcontrollers) |
| Game engines | C++ (Unreal, Unity) |
| High-frequency trading | C++ |
| Web browsers | C++ (Chrome, Firefox) |
| Python extensions | C/C++ (CPython, NumPy) |

## Compiler Flags

```bash
# C
gcc -Wall -Wextra -O2 -o program source.c

# C++
g++ -std=c++20 -Wall -Wextra -O2 -o program source.cpp

# Debug
g++ -g -O0 -fsanitize=address -fsanitize=undefined source.cpp
```

**Links**: [[Memory Management]] | [[Operating Systems]] | [[Rust]] | [[Build Tools]] | [[WebAssembly]]
