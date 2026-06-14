---
id: a1b2c3d4-1092-4000-8000-000000000092
title: Compiler Design
language: markdown
tags: [web-dev, programming, compiler, programming-languages]
selection: null
isPinned: false
timestamp: 1781500001092
---
# Compiler Design

A compiler translates high-level source code into low-level machine code (or bytecode) through a series of phases.

## Pipeline

```
Source → Lexer → Parser → Semantic Analyzer → IR Gen → Optimizer → Code Gen → Target
          │         │            │              │          │           │
        Tokens    AST      Annotated AST       IR       Optimized    Assembly/
                                                   IR       bytecode
```

## Phase 1: Lexical Analysis (Lexer)

Breaks source into tokens (keywords, identifiers, operators, literals).

```python
import re

class Lexer:
    token_specs = [
        ("KEYWORD", r"\b(if|else|while|return)\b"),
        ("IDENTIFIER", r"[a-zA-Z_][a-zA-Z0-9_]*"),
        ("NUMBER", r"\d+"),
        ("OPERATOR", r"[+\-*/=<>!]"),
        ("LPAREN", r"\("),
        ("RPAREN", r"\)"),
        ("SEMICOLON", r";"),
        ("SKIP", r"[ \t\n]+"),
    ]

    def tokenize(self, source):
        tokens = []
        pos = 0
        while pos < len(source):
            for kind, pattern in self.token_specs:
                match = re.match(pattern, source[pos:])
                if match:
                    if kind != "SKIP":
                        tokens.append((kind, match.group()))
                    pos += match.end()
                    break
            else:
                raise SyntaxError(f"Unexpected char at {pos}")
        return tokens
```

## Phase 2: Parsing

Builds an Abstract Syntax Tree (AST) from tokens.

```python
class Parser:
    def __init__(self, tokens):
        self.tokens = tokens
        self.pos = 0

    def parse_expression(self):
        # E → T | E + T
        node = self.parse_term()
        while self.peek() == ("OPERATOR", "+"):
            self.consume()
            right = self.parse_term()
            node = ("Add", node, right)
        return node

    def parse_term(self):
        # T → F | T * F
        node = self.parse_factor()
        while self.peek() == ("OPERATOR", "*"):
            self.consume()
            right = self.parse_factor()
            node = ("Mul", node, right)
        return node

    def parse_factor(self):
        if self.peek()[0] == "NUMBER":
            return ("Num", int(self.consume()[1]))
        if self.peek()[0] == "LPAREN":
            self.consume()
            node = self.parse_expression()
            self.expect("RPAREN")
            return node
```

## Phase 3: Semantic Analysis

Type checking, symbol resolution, scope validation.

```python
class SemanticAnalyzer:
    def __init__(self):
        self.symbol_table = {}

    def analyze(self, ast):
        if ast[0] == "Num":
            return "int"
        elif ast[0] == "Add":
            left = self.analyze(ast[1])
            right = self.analyze(ast[2])
            if left != "int" or right != "int":
                raise TypeError("Addition requires int operands")
            return "int"
```

## Phase 4: Intermediate Representation (IR)

```python
# Three-address code (TAC)
t1 = 5
t2 = 3
t3 = t1 + t2

# SSA (Static Single Assignment) — each variable assigned exactly once
t1 = 5
t2 = 3
t3 = t1 + t2
```

## Phase 5: Optimization

| Pass | Effect |
|------|--------|
| Constant folding | `2 + 3` → `5` |
| Dead code elimination | Remove unused variables |
| Loop invariant hoisting | Move constant expressions out of loops |
| Common subexpression | Reuse computed values |
| Inlining | Replace function call with body |

## Phase 6: Code Generation

```python
# AST → assembly (simplified)
def generate(node):
    if node[0] == "Num":
        return f"MOV {node[1]}, R0"
    elif node[0] == "Add":
        left = generate(node[1])
        right = generate(node[2])
        return f"{left}\n{right}\nADD R0, R1"
```

**Links**: [[Finite Automata and Formal Languages]] | [[Programming Language Paradigms]] | [[Computer Architecture and Organization]] | [[Regular Expressions]] | [[Functional Programming]]
