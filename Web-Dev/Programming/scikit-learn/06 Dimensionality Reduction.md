---
tags: [scikit-learn, dimensionality-reduction, pca, tsne, python]
---

# 06 — Dimensionality Reduction

## Available Methods

| Method | Linear? | For Visualization? | Preserves Distances? |
|--------|---------|-------------------|---------------------|
| `PCA` | Yes | Yes | L2 distances (approx) |
| `TruncatedSVD` | Yes | Sparse data | L2 distances |
| `TSNE` | No | Yes (only viz) | Local structure |
| `Isomap` | No | Sometimes | Geodesic distances |
| `SpectralEmbedding` | No | Sometimes | Affinity/graph |

## Code Examples

```python
from sklearn.decomposition import PCA, TruncatedSVD
from sklearn.manifold import TSNE

# PCA — linear, for denoising and visualization
pca = PCA(n_components=2)
X_2d = pca.fit_transform(X)
explained_variance = pca.explained_variance_ratio_

# t-SNE — non-linear, visualization only
tsne = TSNE(n_components=2, perplexity=30, random_state=42)
X_tsne = tsne.fit_transform(X)
```

## PCA Tips

- Always scale data before PCA (use `StandardScaler`)
- Explained variance ratio tells you how many components to keep
- PCA components are interpretable as weighted combinations of original features

**Links**: [[Web-Dev/Programming/scikit-learn/05 Clustering]] | [[Web-Dev/Programming/scikit-learn/02 Feature Engineering]] | [[Web-Dev/Programming/scikit-learn/03 Classification]]
**See also**: [[Dimensionality Reduction]], [[Feature Stores for Machine Learning]]
