---
id: a1b2c3d4-1026-4000-8000-000000000026
title: Algorithm Paradigms
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001026
---
# Algorithm Paradigms

Algorithm paradigms are general approaches to solving problems. Recognizing which paradigm fits a problem is key to efficient solutions.

## Divide and Conquer

Split problem into smaller subproblems, solve each, combine results.

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)
```

**Examples**: Merge sort, quicksort, binary search, Strassen's matrix multiplication.

**Complexity**: Typically O(n log n) or O(n²) for divide-and-conquer.

## Dynamic Programming

Solve overlapping subproblems by caching results (memoization or tabulation).

```python
# Fibonacci with memoization
def fib(n, memo={}):
    if n in memo:
        return memo[n]
    if n <= 1:
        return n
    memo[n] = fib(n-1, memo) + fib(n-2, memo)
    return memo[n]
```

**Key signs**: Optimal substructure + overlapping subproblems.

**Examples**: Knapsack, longest common subsequence, edit distance, coin change.

## Greedy Algorithms

Make locally optimal choice at each step, hoping for global optimum.

```python
def coin_change_greedy(coins, amount):
    coins.sort(reverse=True)
    count = 0
    for coin in coins:
        count += amount // coin
        amount %= coin
    return count  # works for canonical coin systems
```

**Examples**: Dijkstra, Prim's, Huffman coding, interval scheduling.

**Does not work** when local optimum ≠ global optimum (e.g., non-canonical coin systems).

## Backtracking

Explore all possibilities, pruning dead ends.

```python
def solve_nqueens(n):
    def backtrack(row, cols, diag1, diag2):
        if row == n:
            return 1
        count = 0
        for col in range(n):
            if col in cols or (row - col) in diag1 or (row + col) in diag2:
                continue
            count += backtrack(row + 1, cols | {col}, diag1 | {row - col}, diag2 | {row + col})
        return count
    return backtrack(0, set(), set(), set())
```

**Examples**: N-Queens, Sudoku, subset sum, permutation generation.

## Branch and Bound

Like backtracking but uses a bound to prune suboptimal branches. Used for optimization where exhaustive search is too expensive.

| Paradigm | Use When | Example |
|----------|----------|---------|
| Divide & Conquer | Independent subproblems | Merge sort |
| Dynamic Programming | Overlapping subproblems | Fibonacci |
| Greedy | Local choice = global optimum | Dijkstra |
| Backtracking | Need all solutions | N-Queens |
| Branch & Bound | Optimal solution with pruning | TSP |

## Complexity Classes

| Class | Description |
|-------|-------------|
| P | Solvable in polynomial time |
| NP | Verifiable in polynomial time |
| NP-Complete | NP and every NP problem reduces to it |
| NP-Hard | At least as hard as NP-complete |

**Links**: [[Data Structures]] | [[Graph Algorithms]] | [[Sorting Algorithms]] | [[Big O Notation]] | [[Performance Profiling]]
