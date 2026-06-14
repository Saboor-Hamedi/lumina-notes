---
id: a1b2c3d4-1095-4000-8000-000000000095
title: Computational Complexity
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001095
---
# Computational Complexity

Computational complexity classifies problems by the resources (time, space) required to solve them as input size grows.

## Complexity Classes

| Class | Definition | Example |
|-------|------------|---------|
| P | Solvable in polynomial time | Sorting, shortest path |
| NP | Verifiable in polynomial time | Sudoku, SAT, TSP decision |
| NP-Complete | NP + every NP problem reduces to it | 3-SAT, Knapsack, Hamiltonian path |
| NP-Hard | At least as hard as NP-complete | TSP optimization, Halting problem |
| co-NP | Complement of NP problems | "Formula is not satisfiable" |
| PSPACE | Solvable in polynomial space | QSAT, game playing |

## Reductions

Problem A reduces to B (A ≤ B) if a solution to B can solve A.

```
3-SAT ≤ Independent Set ≤ Vertex Cover ≤ Clique
                                         ↓
                                   TSP ≈ Hamiltonian Path
```

**Cook-Levin Theorem**: SAT is NP-complete (every NP problem reduces to SAT).

## P vs NP (Millennium Problem)

```
P = NP?  (1,000,000 USD reward)

If P = NP: Efficient solutions exist for all verification problems
If P ≠ NP: Some problems are inherently hard

Most researchers believe P ≠ NP.
```

## Important NP-Complete Problems

| Problem | Description | Reduction From |
|---------|-------------|----------------|
| 3-SAT | Boolean formula with 3-literal clauses | SAT |
| Vertex Cover | Cover all edges with k vertices | Independent Set |
| Hamiltonian Cycle | Cycle visiting each vertex once | 3-SAT |
| Subset Sum | Does a subset sum to target? | 3-SAT |
| Graph Coloring | Color graph with k colors | 3-SAT |
| Clique | Find complete subgraph of size k | Independent Set |

## Approximation Algorithms

For NP-hard optimization problems, find near-optimal solutions with guarantees:

| Problem | Approximation Ratio | Algorithm |
|---------|-------------------|-----------|
| Vertex Cover | 2x | Maximal matching |
| TSP (metric) | 1.5x | Christofides algorithm |
| Set Cover | ln(n) | Greedy |
| Knapsack | (1-ε) | FPTAS |

## Fixed-Parameter Tractability (FPT)

Some NP-hard problems are tractable when one parameter is small:

```
Vertex Cover: O(2^k * n) — exponential only in k (cover size)
```

## Space Complexity

| Class | Resource | Example |
|-------|----------|---------|
| L (Logspace) | O(log n) memory | Graph connectivity |
| NL | Nondeterministic logspace | Directed reachability |
| PSPACE | Polynomial space | QSAT, generalized chess |
| EXPSPACE | Exponential space | |

## Randomized Complexity

| Class | Type | Example |
|-------|------|---------|
| RP | Always no, sometimes wrong yes | Primality (Miller-Rabin) |
| ZPP | Never wrong, sometimes slow | |
| BPP | Bounded-error probabilistic | Matrix multiplication check |
| IP | Interactive proofs | Graph non-isomorphism |

## Practical Implications

| Problem Type | What to Do |
|-------------|------------|
| P | Find efficient algorithm (O(n log n), O(n²)) |
| NP-complete (small n) | Branch and bound, SAT solvers |
| NP-complete (large n) | Approximation algorithm, heuristics |
| PSPACE-hard | Heuristics, domain-specific |
| Undecidable | Approximate, restrict problem |

**Links**: [[Algorithm Paradigms]] | [[Big O Notation]] | [[Data Structures]] | [[Graph Algorithms]] | [[Compiler Design]]
