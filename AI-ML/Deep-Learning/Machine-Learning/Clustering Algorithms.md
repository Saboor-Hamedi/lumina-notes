---
id: ml-015-0000-0000-0000-000000000064
title: Clustering Algorithms
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781900000016
---
# Clustering Algorithms

**Links**: [[Dimensionality Reduction]] | [[Feature Engineering]] | [[Recommender Systems]] | [[Vector Databases for RAG]] | [[Data Structures]]

## What is Clustering?

Clustering groups similar data points without labels (unsupervised learning).

## K-Means

Partitions data into K clusters by minimizing within-cluster variance.

```python
from sklearn.cluster import KMeans

kmeans = KMeans(n_clusters=5, random_state=42)
labels = kmeans.fit_predict(X)
centroids = kmeans.cluster_centers_
```

**Choosing K**:

```python
# Elbow method
inertias = []
for k in range(1, 11):
    kmeans = KMeans(n_clusters=k, n_init=10)
    kmeans.fit(X)
    inertias.append(kmeans.inertia_)
```

## DBSCAN

Density-based — finds clusters of arbitrary shape, handles noise.

```python
from sklearn.cluster import DBSCAN

dbscan = DBSCAN(eps=0.5, min_samples=5)
labels = dbscan.fit_predict(X)
# label = -1 means noise/outlier
```

- No need to specify K
- Finds arbitrarily shaped clusters
- Identifies outliers

## Hierarchical Clustering

Builds a tree of clusters (dendrogram).

```python
from sklearn.cluster import AgglomerativeClustering

hierarchical = AgglomerativeClustering(
    n_clusters=5,
    linkage='ward'  # Minimizes variance increase
)
labels = hierarchical.fit_predict(X)
```

## Comparison

| Algorithm | Shape | K needed? | Scalable | Outliers |
|-----------|-------|-----------|----------|----------|
| K-Means | Spherical | Yes | Very high | Sensitive |
| DBSCAN | Arbitrary | No | Medium | Robust |
| Hierarchical | Any | Optional | Low | Moderate |
| Gaussian Mixture | Elliptical | Yes | High | Probabilistic |

**Next**: [[Dimensionality Reduction]] — PCA, t-SNE, UMAP