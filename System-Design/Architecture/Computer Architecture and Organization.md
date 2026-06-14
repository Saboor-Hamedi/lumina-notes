---
id: a1b2c3d4-1065-4000-8000-000000000065
title: Computer Architecture and Organization
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001065
---
# Computer Architecture and Organization

Computer architecture describes how hardware components work together to execute programs — from transistors to instruction sets to memory hierarchy.

## The Memory Hierarchy

```
CPU Registers         → ~1 cycle,     ~500 B
L1 Cache              → ~3 cycles,    ~32 KB
L2 Cache              → ~10 cycles,   ~256 KB
L3 Cache (shared)     → ~40 cycles,   ~8-32 MB
RAM (DRAM)            → ~200 cycles,  ~16-64 GB
SSD                   → ~100K cycles, ~256 GB-2 TB
HDD                   → ~10M cycles,  ~1-10 TB
```

**Key insight**: Most programs spend 90%+ of time waiting for memory, not computing.

## CPU Pipeline

```
Fetch → Decode → Execute → Memory Access → Write Back
  │        │         │           │             │
  │        │         │           │             │
  └────────────────────────────────────────────┘
                (5-stage RISC pipeline)
```

| Hazard | Cause | Mitigation |
|--------|-------|------------|
| Structural | Resource conflict | Separate units, forwarding |
| Data | Instruction depends on previous | Forwarding, stalling |
| Control | Branch prediction | Branch predictor, speculation |

## Instruction Set Architectures (ISA)

| ISA | Type | Examples | Philosophy |
|-----|------|----------|------------|
| x86/x86-64 | CISC | Intel, AMD | Complex instructions, variable length |
| ARM | RISC | Apple M-series, Qualcomm | Simple instructions, fixed length |
| RISC-V | RISC (open) | SiFive, various | Free, extensible |
| WebAssembly | Virtual ISA | Browser | Portable, sandboxed |

## CISC vs RISC

| Aspect | CISC (x86) | RISC (ARM, RISC-V) |
|--------|------------|-------------------|
| Instruction length | Variable | Fixed (32-bit) |
| Instruction count | Many complex | Few simple |
| Cycles per instruction | Variable | 1 (pipelined) |
| Code size | Smaller | Larger |
| Power efficiency | Lower | Higher |

## Cache Architecture

```
Address → Tag + Index + Offset
              ↓
      Tag match? → Hit → return data
           ↓ miss
      Next level → load block → update cache
```

| Type | Behavior |
|------|----------|
| Direct-mapped | Each address maps to exactly one cache line |
| Set-associative | Each address maps to N lines (4-8 way common) |
| Fully associative | Any address anywhere (expensive) |

## Virtual Memory

```
Virtual Address → Page Table → Physical Address
                        ↓
                TLB (Translation Lookaside Buffer)
```

- **Page**: Fixed-size block (4KB typically)
- **Page fault**: Access not in RAM → load from disk
- **MMU**: Hardware that translates addresses

## SIMD (Single Instruction, Multiple Data)

Execute same operation on multiple data points simultaneously:

```
x86: SSE (128-bit), AVX (256-bit), AVX-512 (512-bit)
ARM: NEON (128-bit)
```

```cpp
// AVX2 — 8 integers at once
__m256i a = _mm256_loadu_si256((__m256i*)arr1);
__m256i b = _mm256_loadu_si256((__m256i*)arr2);
__m256i sum = _mm256_add_epi32(a, b);
_mm256_storeu_si256((__m256i*)result, sum);
```

**Links**: [[Operating Systems]] | [[Memory Management]] | [[Performance Profiling]] | [[C and C++]] | [[WebAssembly]]
