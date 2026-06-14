---
id: 23c110d3-c7de-4711-a73d-97ebbfdece07
title: Big O Notation
language: markdown
tags: [system-design, algorithms]
selection: null
isPinned: true
timestamp: 1781226363412
---
# Big O Notation

Big O describes the performance or complexity of an algorithm in terms of input size (n). It measures worst-case time or space growth.

## Common Complexities

| Notation | Name | Example |
|----------|------|---------|
| O(1) | Constant | Array access by index |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Linear search |
| O(n log n) | Linearithmic | Merge sort, Quick sort |
| O(n²) | Quadratic | Bubble sort, nested loops |
| O(2ⁿ) | Exponential | Fibonacci recursive |
| O(n!) | Factorial | Traveling salesman (brute force) |

## Growth Comparison

For n = 1000:
- O(1): 1 operation
- O(log n): ~10 operations
- O(n): 1000 operations
- O(n log n): ~10,000 operations
- O(n²): 1,000,000 operations

Always strive for the lowest complexity that solves the problem correctly.

**See also**: [[Programming Language Paradigms]], [[Code Architecture Patterns]], [[SQL Query Optimization]]
