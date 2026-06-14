---
id: a1b2c3d4-1015-4000-8000-000000000015
title: WebAssembly
language: markdown
tags: [web-dev, wasm, webassembly]
selection: null
isPinned: false
timestamp: 1781500001015
---

**Links**: [[Web Development Fundamentals]] | [[Web Components]] | [[Web Accessibility]] | [[Web3 and Decentralized Applications]] | [[Desktop Apps with Electron and Tauri]] | [[React]]


# WebAssembly

WebAssembly (Wasm) is a binary instruction format for stack-based virtual machines. It runs at near-native speed in browsers and increasingly on servers.

## Key Characteristics

| Feature | Wasm | JavaScript |
|---------|------|------------|
| Format | Binary (`.wasm`) | Text (`.js`) |
| Parsing | Fast (pre-compiled) | Parsed at runtime |
| Performance | Near-native | JIT-compiled |
| Types | Static (i32, i64, f32, f64) | Dynamic |
| Security | Sandboxed, no raw pointers | Sandboxed |

## Wasm vs JS Performance

Wasm excels at:
- CPU-heavy computations (games, image/video processing)
- Porting existing C/C++/Rust libraries
- Scientific computing, cryptography
- 3D rendering, physics simulations

JS still wins for:
- DOM manipulation
- Network I/O
- Business logic with heavy string processing
- Rapid prototyping

## Compilation Targets

Source languages that compile to Wasm:

| Language | Toolchain | Example Use |
|----------|-----------|-------------|
| C/C++ | Emscripten | Port legacy libraries |
| Rust | wasm-pack | Systems programming for web |
| Go | Go compiler (tinygo) | Web apps |
| C# | Blazor | Full-stack .NET in browser |
| Kotlin | Kotlin/Wasm | Kotlin in browser |
| AssemblyScript | asc | TypeScript-like Wasm |

## Wasm System Interface (WASI)

WASI extends Wasm beyond the browser with:
- File system access
- Network sockets
- Clock/time
- Random number generation

Enables running Wasm on servers with `wasmtime`, `wasmer`, `wazero`.

## WebAssembly Components

- **WAT**: Human-readable text format
- **WASM**: Binary format
- **WASI**: System interface for non-browser environments
- **Component Model**: Proposals for interop between Wasm modules

## Example (WAT)

```wat
(module
  (func $add (param $a i32) (param $b i32) (result i32)
    local.get $a
    local.get $b
    i32.add)
  (export "add" (func $add)))
```

## Use Cases

| Domain | Examples |
|--------|---------|
| Browser | Figma, AutoCAD, Photoshop web |
| Compute | Cloudflare Workers, Fastly Compute@Edge |
| Edge | Serverless functions at CDN edge |
| Plugin systems | Extensible sandboxed runtimes |
| Container alternative | Lightweight secure execution |

## Limitations

- No direct DOM access (must go through JS glue)
- Larger binary sizes for complex apps
- Limited debugging tooling (vs native)
- No built-in garbage collector (soon: GC proposal)
- Threading still evolving (shared memory proposal)

**Links**: [[Web Development Fundamentals]] | [[HTTP Protocol]] | [[Build Tools]] | [[Performance Profiling]] | [[Rust Programming Basics]]
