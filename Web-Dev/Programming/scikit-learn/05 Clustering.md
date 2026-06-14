---
tags: [scikit-learn, clustering, unsupervised, python]
---

# 05 — Clustering

## Available Algorithms

| Algorithm | Shape | n_clusters | When to Use |
|-----------|-------|------------|-------------|
| `KMeans` | Spherical | Required | Large datasets, simple clusters |
| `DBSCAN` | Arbitrary | Auto | Noise, non-spherical, varying density |
| `AgglomerativeClustering` | Hierarchical | Required | Dendrogram visualization |
| `GaussianMixture` | Elliptical | Required | Soft assignments, overlapping clusters |
| `Birch` | Spherical | Optional | Very large datasets, streaming |

## Code Examples

```python
from sklearn.cluster import KMeans, DBSCAN, AgglomerativeClustering
from sklearn.mixture import GaussianMixture
from sklearn.metrics import silhouette_score

# K-Means
kmeans = KMeans(n_clusters=5, random_state=42, n_init='auto')
labels = kmeans.fit_predict(X)
centroids = kmeans.cluster_centers_

# Find optimal k using silhouette score
scores = []
for k in range(2, 11):
    kmeans = KMeans(n_clusters=k, random_state=42, n_init='auto')
    labels = kmeans.fit_predict(X)
    scores.append(silhouette_score(X, labels))

# DBSCAN — density-based, finds arbitrary shapes, detects noise
dbscan = DBSCAN(eps=0.5, min_samples=5)
labels = dbscan.fit_predict(X)  # -1 = noise point
```

## Choosing Parameters

- **KMeans**: n_clusters via elbow method or silhouette score
- **DBSCAN**: `eps` (neighborhood radius) and `min_samples` (density threshold)
- **GMM**: n_components via BIC or AIC

**Links**: [[Web-Dev/Programming/scikit-learn/06 Dimensionality Reduction]] | [[Web-Dev/Programming/scikit-learn/08 Evaluation Metrics]] | [[Web-Dev/Programming/scikit-learn/01 API and Preprocessing]]
**See also**: [[Clustering Algorithms]], [[Anomaly Detection]]
