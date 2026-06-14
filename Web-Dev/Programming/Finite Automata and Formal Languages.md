---
id: a1b2c3d4-1091-4000-8000-000000000091
title: Finite Automata and Formal Languages
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001091
---
# Finite Automata and Formal Languages

Finite automata are abstract machines that recognize patterns in strings. They form the theoretical foundation of compilers, regex engines, and protocol validation.

## Chomsky Hierarchy

| Type | Grammar | Language | Automation |
|------|---------|----------|------------|
| Type-0 | Unrestricted | Recursively enumerable | Turing machine |
| Type-1 | Context-sensitive | Context-sensitive | Linear-bounded TM |
| Type-2 | Context-free | Context-free | Pushdown automaton |
| Type-3 | Regular | Regular | Finite automaton |

## Deterministic Finite Automaton (DFA)

```
States: {q0, q1, q2}
Alphabet: {0, 1}
Transitions:
  q0 --0--> q0    q0 --1--> q1
  q1 --0--> q2    q1 --1--> q0
  q2 --0--> q1    q2 --1--> q2
Start: q0
Accept: q0
```

```python
class DFA:
    def __init__(self):
        self.state = "q0"
        self.transitions = {
            ("q0", "0"): "q0",
            ("q0", "1"): "q1",
            ("q1", "0"): "q2",
            ("q1", "1"): "q0",
            ("q2", "0"): "q1",
            ("q2", "1"): "q2",
        }
        self.accept_states = {"q0"}

    def accepts(self, string):
        for symbol in string:
            self.state = self.transitions[(self.state, symbol)]
        return self.state in self.accept_states
```

## NFA vs DFA

| Aspect | DFA | NFA |
|--------|-----|-----|
| Transitions | Exactly one per symbol per state | Zero or more (including epsilon) |
| Deterministic? | Yes | No (choices) |
| States | Can be larger (exponential blowup) | Generally fewer |
| Implementation | Simple (table lookup) | Backtracking or subset construction |
| Power | Same as NFA | Same as DFA |

## Regular Expressions and Automata

```
Regex: (ab|cd)*
NFA:   ε → [a] → [b] → ε → [loop]
       ε → [c] → [d] → ε
DFA:   Subset construction of the NFA
```

**Thompson's construction**: Every regex can be converted to an equivalent NFA.

## Context-Free Grammars (CFG)

```
S → aSb | ε     (generates balanced parentheses, a^n b^n)

Expression grammar:
E → E + T | T
T → T * F | F
F → ( E ) | id
```

```python
class PushdownAutomaton:
    def __init__(self):
        self.stack = ["Z0"]  # bottom marker

    def accepts(self, string):
        # PDA for a^n b^n
        for symbol in string:
            if symbol == "a":
                self.stack.append("a")
            elif symbol == "b":
                if not self.stack or self.stack[-1] == "Z0":
                    return False
                self.stack.pop()
        return self.stack == ["Z0"]
```

## Pumping Lemma

Used to prove a language is NOT regular or NOT context-free:

**Regular pumping lemma**: For any regular language L, there exists p such that any string s with |s| ≥ p can be split as s = xyz where |xy| ≤ p, |y| > 0, and xyⁱz ∈ L for all i ≥ 0.

## Applications

| Area | Application |
|------|-------------|
| Compilers | Lexing (DFA), Parsing (PDA), Code gen (Turing) |
| Network protocols | State machines for TCP, HTTP |
| Regex engines | NFA simulation (backtracking) |
| Natural language | CFGs for syntax trees |
| Model checking | State space exploration |
| Digital circuits | Finite state machines |

**Links**: [[Regular Expressions]] | [[Compiler Design]] | [[Algorithm Paradigms]] | [[Data Structures]] | [[Big O Notation]]
