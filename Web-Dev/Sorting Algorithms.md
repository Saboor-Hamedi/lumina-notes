---
id: a1b2c3d4-0015-4000-8000-000000000015
title: Sorting Algorithms
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781400000015
---
# Sorting Algorithms

Sorting arranges data in a specified order. Different algorithms trade off speed, memory, and stability.

## Comparison-Based

| Algorithm | Best | Average | Worst | Memory | Stable |
|-----------|------|---------|-------|--------|--------|
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) | No |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | O(1) | No |
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |

## Non-Comparison Based

| Algorithm | Time | Space | Constraint |
|-----------|------|-------|------------|
| Counting Sort | O(n + k) | O(k) | Integer range k |
| Radix Sort | O(d * (n + k)) | O(n + k) | Fixed digit length |
| Bucket Sort | O(n + k) avg | O(n) | Uniform distribution |

**See also**: [[Big O Notation]], [[Data Structures]], [[Functional Programming]]
