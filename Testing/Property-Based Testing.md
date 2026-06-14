---
id: a1b2c3d4-1070-4000-8000-000000000070
title: Property-Based Testing
language: markdown
tags: [testing, property-based-testing]
selection: null
isPinned: false
timestamp: 1781500001070
---

**Links**: [[Mutation Testing]] | [[Unit Testing Guide]] | [[Test-Driven Development]] | [[Software Testing Pyramid]] | [[Snapshot Testing]] | [[Integration Testing Patterns]]


# Property-Based Testing

Property-based testing defines properties that should always hold, then automatically generates random inputs to verify them — catching edge cases that example-based tests miss.

## Example-Based vs Property-Based

| Aspect | Example-Based | Property-Based |
|--------|--------------|----------------|
| Approach | Test specific inputs | Test general properties |
| Coverage | Limited (you think of) | Exhaustive (generated) |
| Inputs | Manually chosen | Randomly generated |
| Bug finding | Known edge cases | Surprising edge cases |
| Framework | JUnit, pytest | Hypothesis, QuickCheck |

## Core Concept

```python
# Example-based: test specific cases
def test_reverse():
    assert reverse([1, 2, 3]) == [3, 2, 1]
    assert reverse([]) == []
    assert reverse([1]) == [1]

# Property-based: test general properties
from hypothesis import given, strategies as st

@given(st.lists(st.integers()))
def test_reverse_properties(lst):
    assert reverse(reverse(lst)) == lst        # involution
    assert len(reverse(lst)) == len(lst)        # same length
    assert reverse(lst)[0] == lst[-1] if lst else True  # first element
```

## Common Property Patterns

| Pattern | Property | Example |
|---------|----------|---------|
| Involution | Applying twice returns original | `reverse(reverse(x)) == x` |
| Idempotence | Applying twice same as once | `sort(sort(x)) == sort(x)` |
| Roundtrip | Encode → decode gives original | `decode(encode(x)) == x` |
| Metamorphic | Related inputs → related outputs | `sort(x) + sort(y) == sort(x + y)` |
| Monotonicity | Order preserved | If x ≤ y then `f(x) ≤ f(y)` |
| Distinct | No duplicates in result | `len(set(result)) == len(result)` |

## Hypothesis (Python)

```python
from hypothesis import given, strategies as st, assume, settings

# Custom strategies
usernames = st.text(min_size=3, max_size=30, alphabet=st.characters(whitelist_categories=['L', 'N']))

@given(st.lists(st.integers(min_value=0, max_value=1000)))
@settings(max_examples=200)
def test_sort_is_stable(lst):
    result = sorted(lst)
    assert all(result[i] <= result[i+1] for i in range(len(result)-1))
    assert sorted(result) == result  # idempotent

@given(st.dictionaries(st.text(), st.integers()))
def test_dict_operations(d):
    key = next(iter(d.keys())) if d else "missing"
    assume(key in d)  # precondition
    assert d[key] == d.get(key)

@given(st.lists(st.integers()))
def test_sort_preserves_elements(lst):
    result = sorted(lst)
    assert sorted(result) == sorted(lst)
    assert len(result) == len(lst)
```

## Shrinking

When a test fails, Hypothesis finds the minimal failing input:

```
Failing input: [-384, -127, 992, 3, ...]  (100 elements)
Shrinking...
Final: [1, 0, -1]  (minimal case)
```

**Shrinking** tries removing elements, simplifying values, and reducing magnitude to produce the simplest failing case.

## QuickCheck (Haskell) — The Original

```haskell
import Test.QuickCheck

prop_reverse :: [Int] -> Bool
prop_reverse xs = reverse (reverse xs) == xs

prop_reverse_append :: [Int] -> [Int] -> Bool
prop_reverse_append xs ys = reverse (xs ++ ys) == reverse ys ++ reverse xs

-- Run: quickCheck prop_reverse
-- Run: quickCheck prop_reverse_append
```

## Stateful Testing

Test sequences of operations for consistency:

```python
@given(st.lists(st.tuples(st.just("push"), st.integers()) | st.just(("pop", None))))
def test_stack_operations(operations):
    stack = []
    model = []
    for op, val in operations:
        if op == "push":
            stack.append(val)
            model.append(val)
        else:  # pop
            if model:
                assert stack.pop() == model.pop()
            else:
                with pytest.raises(IndexError):
                    stack.pop()
    assert stack == model
```

## Integration with Unit Testing

```python
import pytest
from hypothesis import given, strategies as st

class TestSorting:
    @given(st.lists(st.integers()))
    def test_timsort(self, lst):
        assert sorted(lst) == sorted(sorted(lst))  # idempotent

    @given(st.lists(st.integers()))
    def test_mergesort(self, lst):
        result = merge_sort(lst.copy())
        assert len(result) == len(lst)
        assert all(result[i] <= result[i+1] for i in range(len(result)-1))
```

## When to Use Property-Based Testing

| Do Use | Don't Use |
|--------|-----------|
| Pure functions | UI/visual tests |
| Data structures | Complex integration flows |
| Parsers/serializers | Tests requiring specific setup |
| Sorting, searching | Hard-to-define properties |
| Numerical algorithms | Nondeterministic code |

**Links**: [[Unit Testing Guide]] | [[Software Testing Pyramid]] | [[Debugging Strategies]] | [[Algorithm Paradigms]] | [[Data Structures]] | [[Mutation Testing]] | [[Functional Programming Concepts]]

**See also**: [[Data Serialization]], [[Compiler Design]] (roundtrip tests), [[Software Design Principles]]
