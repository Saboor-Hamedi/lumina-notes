---
id: a1b2c3d4-1019-4000-8000-000000000019
title: Graph Algorithms
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001019
---
# Graph Algorithms

Graphs model relationships between entities. They are fundamental to networking, social media, maps, dependency resolution, and recommendation systems.

## Graph Representations

| Representation | Space | Edge Lookup | Add Edge |
|---------------|-------|-------------|----------|
| Adjacency Matrix | O(V²) | O(1) | O(1) |
| Adjacency List | O(V+E) | O(deg(V)) | O(1) |
| Edge List | O(E) | O(E) | O(1) |

```python
# Adjacency list
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'E'],
    'D': ['B'],
    'E': ['C']
}
```

## Graph Traversal

### Breadth-First Search (BFS)

```python
from collections import deque
def bfs(graph, start):
    visited = set()
    queue = deque([start])
    while queue:
        node = queue.popleft()
        if node not in visited:
            visited.add(node)
            queue.extend(graph[node])
    return visited
```

- Finds shortest path in unweighted graphs
- O(V+E) time, O(V) space

### Depth-First Search (DFS)

```python
def dfs(graph, node, visited=None):
    if visited is None:
        visited = set()
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)
    return visited
```

- Uses stack (recursive or explicit)
- Good for: topological sort, cycle detection, connectivity

## Shortest Path

| Algorithm | Type | Complexity | Use Case |
|-----------|------|------------|----------|
| Dijkstra | Weighted, no negative | O(V²) or O(E+V log V) | Road networks, routing |
| Bellman-Ford | Weighted, negative edges | O(V×E) | Detecting negative cycles |
| Floyd-Warshall | All-pairs | O(V³) | Dense graphs, small V |
| A* | Weighted + heuristic | Depends on heuristic | Pathfinding with goals |

## Minimum Spanning Tree

| Algorithm | Strategy | Complexity |
|-----------|----------|------------|
| Kruskal | Sort edges, union-find | O(E log V) |
| Prim | Grow from seed node | O(V²) or O(E log V) |

## Advanced Algorithms

| Algorithm | Purpose | Notes |
|-----------|---------|-------|
| Topological Sort | Order DAG nodes | Dependency resolution |
| Tarjan's SCC | Strongly connected components | O(V+E) |
| Kosaraju's SCC | Strongly connected components | Two DFS passes |
| Union-Find (DSU) | Connected components, cycles | Near O(1) amortized |
| PageRank | Node importance | Web search ranking |
| Graph Coloring | Assign colors to adjacent nodes | Register allocation |

## Cycle Detection

| Graph Type | Method |
|------------|--------|
| Undirected | Union-Find or DFS with parent check |
| Directed | DFS with back-edge detection (ancestor in current path) |

## Time Complexities

| Graph | V | E | BFS/DFS |
|-------|---|----|---------|
| Sparse | n | O(n) | O(n) |
| Dense | n | O(n²) | O(n²) |
| Tree | n | n-1 | O(n) |

**Links**: [[Data Structures]] | [[Sorting Algorithms]] | [[Big O Notation]] | [[Recommender Systems]] | [[Social Network Analysis]]
