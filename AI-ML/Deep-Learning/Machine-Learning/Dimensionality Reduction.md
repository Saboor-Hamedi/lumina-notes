---
id: ml-016-0000-0000-0000-000000000065
title: Dimensionality Reduction
language: markdown
tags: [ai-ml, machine-learning, dimensionality-reduction, pca, unsupervised]
selection: null
isPinned: false
timestamp: 1781900000017
---
# Dimensionality Reduction

**Links**: [[Feature Engineering]] | [[Clustering Algorithms]] | [[Word Embeddings]] | [[Text Embedding Models]] | [[Data Visualization]]

## What is Dimensionality Reduction?

Reducing the number of features while preserving as much information as possible. Used for visualization, noise reduction, and speeding up models.

## PCA (Principal Component Analysis)

Finds directions of maximum variance and projects data onto them.

```python
from sklearn.decomposition import PCA
import matplotlib.pyplot as plt

pca = PCA(n_components=2)  # Reduce to 2D for visualization
X_reduced = pca.fit_transform(X)

plt.scatter(X_reduced[:, 0], X_reduced[:, 1], c=labels)
plt.xlabel('PC1')
plt.ylabel('PC2')

# Explained variance ratio
print(pca.explained_variance_ratio_)
```

## t-SNE

Non-linear method great for visualization, preserves local structure.

```python
from sklearn.manifold import TSNE

tsne = TSNE(n_components=2, perplexity=30, random_state=42)
X_tsne = tsne.fit_transform(X)
```

Slower, non-deterministic, good for exploration.

## UMAP

Faster than t-SNE, better at preserving global structure.

```python
import umap

reducer = umap.UMAP(n_components=2, min_dist=0.1)
X_umap = reducer.fit_transform(X)
```

## Comparison

| Method | Linear | Preserves | Speed | Use Case |
|--------|--------|-----------|-------|----------|
| PCA | Yes | Global variance | Fast | Preprocessing, decorrelation |
| t-SNE | No | Local neighborhoods | Slow | Visualization |
| UMAP | No | Local + global | Medium | Visualization + clustering |

**Next**: [[Clustering Algorithms]] — Grouping unlabeled data
