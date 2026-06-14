---
id: a1b2c3d4-1006-4000-8000-000000000006
title: Refactoring Techniques
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001006
---
# Refactoring Techniques

Refactoring restructures existing code without changing its external behavior to improve readability, maintainability, and performance.

## Code Smells (when to refactor)

| Smell | Symptom | Fix |
|-------|---------|-----|
| Long Method | Too many responsibilities | Extract methods |
| Large Class | Too many fields/methods | Extract class/module |
| Duplicated Code | Same logic in multiple places | Extract function, pull up |
| Long Parameter List | 3+ parameters | Introduce parameter object |
| Feature Envy | A method uses more of another class | Move method |
| Shotgun Surgery | One change affects many classes | Move + consolidate |
| Primitive Obsession | Using primitives instead of objects | Introduce value object |

## Composing Methods

| Technique | What It Does |
|-----------|-------------|
| Extract Method | Turn code block into named function |
| Inline Method | Replace trivial function with its body |
| Extract Variable | Name a complex expression |
| Inline Temp | Replace temp variable with expression |
| Replace Temp with Query | Compute value in a method instead of temp |

## Moving Features

| Technique | What It Does |
|-----------|-------------|
| Move Method | Put method on the class it uses most |
| Move Field | Put field on the class that owns it |
| Extract Class | Split a class into two with separate concerns |
| Inline Class | Merge a trivial class into its main user |

## Simplifying Conditionals

| Technique | Before | After |
|-----------|--------|-------|
| Decompose Conditional | Complex if/else | Extract conditions into methods |
| Consolidate Conditional | Multiple ifs, same result | Merge into single expression |
| Replace Nested with Guard | Deeply nested ifs | Early returns |
| Introduce Null Object | Null checks everywhere | Null-safe default object |

## Data Organization

| Technique | Purpose |
|-----------|---------|
| Rename Field/Variable | Clarify meaning |
| Encapsulate Field | Add getter/setter |
| Replace Magic Number | Use named constant |
| Self-Encapsulate Field | Access field through getter in its own class |

## Safe Refactoring Workflow

1. **Ensure tests pass** before starting
2. Make one small change at a time
3. **Run tests** after each change
4. Commit after each safe transformation
5. If tests fail, revert the last change

**Links**: [[Clean Code Principles]] | [[Software Design Principles]] | [[Code Review Best Practices]] | [[Error Handling Patterns]] | [[Unit Testing Guide]]
