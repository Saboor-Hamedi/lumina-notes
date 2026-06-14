---
id: a1b2c3d4-1038-4000-8000-000000000038
title: Coding Interview Patterns
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001038
---
# Coding Interview Patterns

Many coding interview problems reduce to a small set of recurring patterns. Recognizing the pattern is half the solution.

## Sliding Window

Used for subarray/substring problems with contiguous elements.

```python
def max_subarray_sum(nums, k):
    window_sum = sum(nums[:k])
    max_sum = window_sum
    for i in range(k, len(nums)):
        window_sum += nums[i] - nums[i - k]
        max_sum = max(max_sum, window_sum)
    return max_sum
```

**When**: "longest/shortest substring", "maximum sum subarray of size k"

## Two Pointers

Used with sorted arrays or linked lists.

```python
def two_sum_sorted(nums, target):
    left, right = 0, len(nums) - 1
    while left < right:
        s = nums[left] + nums[right]
        if s == target:
            return [left, right]
        if s < target:
            left += 1
        else:
            right -= 1
    return []
```

**When**: "find pair that sums to target", "remove duplicates", "palindrome check"

## Fast and Slow Pointers (Hare & Tortoise)

Used for cycle detection in linked lists.

```python
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```

**When**: "detect cycle", "find middle", "find start of cycle"

## Binary Search

Used on sorted data or monotonic functions.

```python
def binary_search(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] == target:
            return mid
        if nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

**Variants**: Find first/last occurrence, find in rotated array, search space.

## BFS / DFS

Used for tree, graph, and matrix traversal problems.

| Pattern | Use Case | Data Structure |
|---------|----------|----------------|
| BFS | Shortest path, level-order | Queue |
| DFS | All paths, connectivity, backtracking | Stack/recursion |

## Top K Elements (Heap)

```python
import heapq
def top_k_frequent(nums, k):
    freq = Counter(nums)
    return heapq.nlargest(k, freq.keys(), key=freq.get)
```

**When**: "K largest/smallest", "top K frequent elements"

## Prefix Sum

```python
prefix = [0]
for num in nums:
    prefix.append(prefix[-1] + num)
# subarray sum i..j = prefix[j+1] - prefix[i]
```

**When**: "range sum queries", "subarray sum equals k"

## Backtracking

```python
def subsets(nums):
    result = []
    def backtrack(start, path):
        result.append(path[:])
        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()
    backtrack(0, [])
    return result
```

**When**: "all combinations", "permutations", "subsets", "word search"

## Pattern Cheat Sheet

| Pattern | When to Use | Complexity |
|---------|-------------|------------|
| Sliding Window | Contiguous subarray/substring | O(n) |
| Two Pointers | Sorted array | O(n) |
| Fast & Slow | Linked list cycle | O(n) |
| Binary Search | Sorted data, find boundary | O(log n) |
| BFS | Shortest path, level order | O(V+E) |
| DFS | All paths, connected components | O(V+E) |
| Top K | Frequency/order statistics | O(n log k) |
| Prefix Sum | Range sum queries | O(n) / O(1) query |
| Backtracking | All solutions | O(2ⁿ) or O(n!) |
| Trie | String prefix search | O(L) per word |

**Links**: [[Algorithm Paradigms]] | [[Data Structures]] | [[Graph Algorithms]] | [[Sorting Algorithms]] | [[Big O Notation]]
