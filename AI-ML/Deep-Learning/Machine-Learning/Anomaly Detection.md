---
id: a1b2c3d4-1119-4000-8000-000000000119
title: Anomaly Detection
language: markdown
tags: [ai-ml, machine-learning, anomaly-detection, unsupervised]
selection: null
isPinned: false
timestamp: 1781500001119
---
# Anomaly Detection

Anomaly detection identifies data points, events, or patterns that deviate significantly from the norm. It's used for fraud, intrusion detection, and system monitoring.

## Types of Anomalies

| Type | Description | Example |
|------|-------------|---------|
| Point anomaly | Single data point deviates | Credit card charge 100x normal |
| Contextual anomaly | Anomalous in context (time/location) | 30°C in winter (normal in summer) |
| Collective anomaly | Set of points collectively anomalous | Botnet DDoS traffic pattern |

## Detection Approaches

| Category | Methods | Label Requirement |
|----------|---------|------------------|
| Supervised | Classification (Random Forest, XGBoost) | Labeled normal + anomaly |
| Semi-supervised | One-class SVM, Isolation Forest | Only normal labels |
| Unsupervised | Clustering, density-based, autoencoders | No labels |

## Statistical Methods

```python
import numpy as np
from scipy import stats

# Z-score (assumes normal distribution)
def z_score_detection(data, threshold=3):
    z = np.abs(stats.zscore(data))
    return np.where(z > threshold)[0]

# IQR (no distribution assumption)
def iqr_detection(data, multiplier=1.5):
    q1, q3 = np.percentile(data, [25, 75])
    iqr = q3 - q1
    lower = q1 - multiplier * iqr
    upper = q3 + multiplier * iqr
    return np.where((data < lower) | (data > upper))[0]

# Grubbs' test for single outlier
def grubbs_test(data, alpha=0.05):
    mean, std = np.mean(data), np.std(data)
    g = max(np.abs(data - mean)) / std
    critical = (len(data) - 1) / np.sqrt(len(data)) * np.sqrt(...)
    return g > critical
```

## Machine Learning Methods

### Isolation Forest

```python
from sklearn.ensemble import IsolationForest

model = IsolationForest(
    contamination=0.01,  # expected % of anomalies
    random_state=42
)
predictions = model.fit_predict(X)  # 1 = normal, -1 = anomaly
```

**How it works**: Randomly split features. Anomalies are isolated in fewer splits (shorter path length).

### One-Class SVM

```python
from sklearn.svm import OneClassSVM

model = OneClassSVM(nu=0.01, kernel="rbf", gamma="auto")
predictions = model.fit_predict(X)
```

**How it works**: Finds a boundary around normal data. Points outside are anomalies.

### Autoencoder

```python
import torch.nn as nn

class AnomalyAutoencoder(nn.Module):
    def __init__(self, input_dim):
        super().__init__()
        self.encoder = nn.Sequential(
            nn.Linear(input_dim, 64),
            nn.ReLU(),
            nn.Linear(64, 16),
            nn.ReLU(),
            nn.Linear(16, 4),
        )
        self.decoder = nn.Sequential(
            nn.Linear(4, 16),
            nn.ReLU(),
            nn.Linear(16, 64),
            nn.ReLU(),
            nn.Linear(64, input_dim),
        )

    def forward(self, x):
        return self.decoder(self.encoder(x))

# Anomaly = high reconstruction error
reconstruction = autoencoder(X)
error = torch.mean((X - reconstruction) ** 2, dim=1)
anomalies = error > threshold
```

## Time-Series Anomaly Detection

| Method | Approach |
|--------|----------|
| Moving average | Point deviates from rolling window mean |
| Exponential smoothing | Weighted average with decay |
| 3-sigma | Point outside 3 standard deviations from recent window |
| Prophet | Decompose trend + seasonality, flag residuals |
| LSTM | Predict next value, high prediction error = anomaly |
| Numenta (HTM) | Cortical learning algorithm for streaming data |

```python
# Simple moving window
def time_series_anomaly(series, window=10, threshold=3):
    rolling_mean = series.rolling(window=window).mean()
    rolling_std = series.rolling(window=window).std()
    z_scores = (series - rolling_mean) / rolling_std
    return z_scores.abs() > threshold
```

## Evaluation

| Metric | Description | When to Use |
|--------|-------------|-------------|
| Precision | TP / (TP + FP) | When false alarms are costly |
| Recall | TP / (TP + FN) | When missing anomalies is costly |
| F1 | Harmonic mean | Balancing both |
| AUC-ROC | Area under ROC curve | Threshold-independent |

**Challenge**: Anomaly detection suffers from severe class imbalance (typically 0.1-1%).

## Best Practices

- **Normalize** features before detection
- **Contamination rate**: Estimate from domain knowledge or validation
- **Threshold tuning**: Use validation set to find optimal cutoff
- **Context matters**: Consider time of day, day of week for time series
- **Ensemble**: Combine multiple detection methods
- **Online learning**: Update models incrementally for drift
- **Explainability**: Understand why a point was flagged (SHAP, LIME)

**Links**: [[Clustering Algorithms]] | [[Dimensionality Reduction]] | [[Time Series Analysis]] | [[Decision Trees and Random Forests]] | [[Evaluation of RAG Systems]] | [[Monitoring and Observability]] | [[Prometheus and Monitoring Systems]]

**See also**: [[Site Reliability Engineering]], [[Chaos Engineering]], [[Model Monitoring in Production]]
