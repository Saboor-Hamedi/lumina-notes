---
id: a1b2c3d4-1122-4000-8000-000000000122
title: Graph Neural Networks
language: markdown
tags: [ai-ml, machine-learning, gnn, graph-neural-networks]
selection: null
isPinned: false
timestamp: 1781500001122
---
# Graph Neural Networks

Graph Neural Networks (GNNs) apply deep learning to graph-structured data — learning from the relationships and features of nodes, edges, and the graph structure.

## When to Use GNNs

| Domain | Graph Structure | Task |
|--------|----------------|------|
| Social networks | Users → friends | Friend recommendation |
| Molecules | Atoms → bonds | Property prediction |
| Knowledge graphs | Entities → relations | Link prediction |
| Citation networks | Papers → citations | Paper classification |
| Code | AST nodes → edges | Code completion |
| Recommender systems | Users → items | Rating prediction |

## Message Passing Framework

```
Node → Aggregate messages from neighbors → Update node representation → Repeat
```

```python
def message_passing_step(node_features, adjacency_matrix, W):
    # 1. Message: each node sends its features to neighbors
    messages = adjacency_matrix @ node_features  # gather neighbor features

    # 2. Aggregate: sum/mean/max of neighbor messages
    aggregated = messages.mean(dim=1)  # mean aggregation

    # 3. Update: combine node's own features + aggregated messages
    h_new = ReLU(W @ torch.cat([node_features, aggregated], dim=1))

    return h_new
```

## GNN Variants

| Model | Aggregation | Key Idea |
|-------|-------------|----------|
| GCN | Normalized mean | Weight neighbors by degree (Kipf & Welling, 2017) |
| GAT | Attention-weighted | Learn importance of each neighbor (Veličković, 2018) |
| GraphSAGE | Max/Mean/LSTM | Sample neighbors, inductive training (Hamilton, 2017) |
| GIN | Sum + MLP | As powerful as WL graph isomorphism test (Xu, 2019) |
| MPNN | General message/update | Framework (Gilmer, 2017) |

## GCN Implementation

```python
import torch
import torch.nn as nn
import torch.nn.functional as F

class GCNLayer(nn.Module):
    def __init__(self, in_dim, out_dim):
        super().__init__()
        self.W = nn.Linear(in_dim, out_dim)

    def forward(self, x, adj):
        # x: [N, in_dim], adj: [N, N] (normalized)
        # GCN propagation: H' = σ(Â @ X @ W)
        support = self.W(x)           # [N, out_dim]
        output = adj @ support        # message passing
        return F.relu(output)

class GCN(nn.Module):
    def __init__(self, in_dim, hidden_dim, out_dim):
        super().__init__()
        self.conv1 = GCNLayer(in_dim, hidden_dim)
        self.conv2 = GCNLayer(hidden_dim, out_dim)

    def forward(self, x, adj):
        h = self.conv1(x, adj)
        h = self.conv2(h, adj)
        return F.log_softmax(h, dim=1)
```

## GAT (Graph Attention)

```python
class GATLayer(nn.Module):
    def __init__(self, in_dim, out_dim):
        super().__init__()
        self.W = nn.Linear(in_dim, out_dim)
        self.a = nn.Parameter(torch.randn(2 * out_dim, 1))

    def forward(self, x, adj):
        h = self.W(x)  # [N, out_dim]
        N = h.shape[0]

        # Compute attention scores (pairwise)
        h_i = h.unsqueeze(1).expand(-1, N, -1)   # [N, N, out_dim]
        h_j = h.unsqueeze(0).expand(N, -1, -1)   # [N, N, out_dim]
        e = F.leaky_relu(torch.cat([h_i, h_j], dim=2) @ self.a).squeeze(-1)

        # Mask non-neighbors & softmax
        e = e.masked_fill(adj == 0, -1e9)
        alpha = F.softmax(e, dim=1)  # attention weights

        return F.relu(alpha @ h)  # weighted aggregation
```

## Graph-Level Tasks

| Task | Goal | Example |
|------|------|---------|
| Node classification | Predict node label | Paper topic classification |
| Link prediction | Predict edge existence | Friend recommendation |
| Graph classification | Predict graph label | Molecule toxicity |
| Node regression | Predict node value | Age prediction |

## Oversmoothing Problem

As GNN layers increase, node representations become indistinguishable:

```
1 layer: node sees 1-hop neighbors
2 layers: node sees 2-hop neighbors
K layers: node sees entire graph (all nodes look similar)
```

**Mitigations**: Skip connections, normalization, PairNorm, DropEdge.

## Scalability

| Approach | How | Best For |
|----------|-----|----------|
| Full-batch | Entire graph in memory | Small graphs (< 100K nodes) |
| Neighbor sampling | Sample K neighbors per node | Large graphs (GraphSAGE) |
| Cluster-GCN | Partition into subgraphs | Very large graphs |
| GraphSAINT | Sample subgraphs | Billion-scale graphs |

## Libraries

| Library | Framework | Features |
|---------|-----------|----------|
| PyTorch Geometric (PyG) | PyTorch | Comprehensive, fast |
| DGL | PyTorch/TF | Flexible, distributed |
| Jraph | JAX | Differentiable, JIT-compiled |

**Links**: [[Graph Algorithms]] | [[Recommender Systems]] | [[Deep Learning]] | [[NLP with Graphs]] | [[Neural Architecture Search]]
