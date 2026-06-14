---
id: a1b2c3d4-1007-4000-8000-000000000007
title: Debugging Strategies
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001007
---
# Debugging Strategies

Debugging is the systematic process of identifying, isolating, and fixing bugs. A structured approach is faster than guessing.

## The Scientific Method

1. **Observe**: Gather data about the failure
2. **Hypothesize**: Form a theory about the root cause
3. **Predict**: What would confirm or disprove your theory?
4. **Experiment**: Run a test (log, change code, add assertion)
5. **Repeat**: Narrow down until you find the exact cause

## Debugging Techniques

| Technique | When to Use |
|-----------|-------------|
| Print Debugging | Quick feedback, simple issues |
| Log Analysis | Complex systems, production issues |
| Interactive Debugger | Step through code, inspect state |
| Binary Search | Narrow down commit (git bisect) |
| Rubber Duck | Explain problem aloud to find insight |
| Delta Debugging | Minimize failing input/code |

## Reproduce First

Before debugging, ensure you can **reliably reproduce** the bug. A non-reproducible bug might be:

- Race condition
- Environment-specific
- Data-dependent
- Transient network issue

## Divide and Conquer

```
Input Data → [Module A] → [Module B] → [Module C] → Output
                ↓              ↓            ↓
          Check output A   Check B      Check C
```

Insert checkpoints at module boundaries to isolate which component fails.

## Common Bug Categories

| Category | Examples |
|----------|----------|
| Null/undefined | Null pointer, missing property |
| Off-by-one | Loop limits, fencepost errors |
| Race condition | Shared mutable state, timing |
| Type confusion | Wrong type passed or returned |
| Resource leak | Unclosed files, sockets, connections |
| Logic error | Wrong operator, inverted condition |
| Configuration | Wrong environment, missing flag |

## Debugging Tools

| Tool | Use |
|------|-----|
| pdb/ipdb | Python debugger |
| gdb/lldb | C/C++ debugger |
| Chrome DevTools | JavaScript debugging |
| strace/dtrace | System call tracing |
| Wireshark | Network packet analysis |
| git bisect | Find which commit introduced bug |

## Prevention

- Write tests before fixing (regression test)
- Add assertions for invariants
- Improve error messages
- Consider adding observability (metrics, traces)

**Links**: [[Error Handling Patterns]] | [[Unit Testing Guide]] | [[Git Bisect]] | [[Git Blame]] | [[Monitoring and Observability]]
