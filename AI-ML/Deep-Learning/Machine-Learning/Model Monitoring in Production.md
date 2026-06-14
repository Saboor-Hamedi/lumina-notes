---
id: a1b2c3d4-1175-4000-8000-000000000175
title: Model Monitoring in Production
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001175
---
# Model Monitoring in Production

Model monitoring tracks ML model performance, data drift, and system health in production. It detects when models degrade and triggers retraining or alerts.

## What to Monitor

| Category | Metrics | Detection Method |
|----------|---------|-----------------|
| Data Drift | Feature distributions | KS test, PSI, JS divergence |
| Prediction Drift | Output distribution | Same statistical tests |
| Concept Drift | Model accuracy | Ground truth comparison |
| Performance | Latency, throughput | System metrics |
| Fairness | Demographic parity | Bias metrics |
| Model Health | Error rate, NaN rate | Threshold-based |

## Data Drift Detection

```python
import numpy as np
from scipy.stats import ks_2samp
from scipy.spatial.distance import jensenshannon

def detect_drift(reference, current, threshold=0.05):
    """Detect feature drift using KS test."""
    statistic, p_value = ks_2samp(reference, current)
    return {
        "drifted": p_value < threshold,
        "p_value": p_value,
        "statistic": statistic,
    }

def population_stability_index(expected, actual, bins=10):
    """PSI measures distribution shift."""
    expected_hist, _ = np.histogram(expected, bins=bins)
    actual_hist, _ = np.histogram(actual, bins=bins)

    expected_pct = expected_hist / len(expected) + 1e-10
    actual_pct = actual_hist / len(actual) + 1e-10

    psi = np.sum((actual_pct - expected_pct) * np.log(actual_pct / expected_pct))
    return psi  # PSI < 0.1: no shift, 0.1-0.25: minor, > 0.25: significant
```

## Model Performance Monitoring

```python
class ModelMonitor:
    def __init__(self, model_id, feature_names):
        self.model_id = model_id
        self.feature_names = feature_names
        self.baseline_stats = None
        self.reference_predictions = []

    def log_prediction(self, features, prediction, metadata=None):
        """Log prediction for monitoring."""
        record = {
            "model_id": self.model_id,
            "features": features,
            "prediction": prediction,
            "timestamp": datetime.now().isoformat(),
            **metadata,
        }
        self._write_to_store(record)

    def log_ground_truth(self, prediction_id, actual):
        """Log actual outcome (for concept drift)."""
        record = {
            "model_id": self.model_id,
            "prediction_id": prediction_id,
            "actual": actual,
            "timestamp": datetime.now().isoformat(),
        }
        self._write_to_store(record)

    def compute_accuracy(self, window_hours=24):
        """Compute accuracy over recent predictions."""
        predictions = self._query_predictions(hours=window_hours)
        if not predictions:
            return None
        correct = sum(p["prediction"] == p["actual"] for p in predictions if "actual" in p)
        total = sum(1 for p in predictions if "actual" in p)
        return correct / total if total > 0 else None

    def alert_if_needed(self, metrics):
        """Check thresholds and alert."""
        alerts = []
        if metrics.get("accuracy", 1.0) < 0.8:
            alerts.append(f"Accuracy dropped to {metrics['accuracy']:.2f}")
        if metrics.get("psi", 0) > 0.25:
            alerts.append(f"PSI {metrics['psi']:.2f} exceeds threshold")
        if metrics.get("latency_p99", 0) > 1000:
            alerts.append(f"P99 latency {metrics['latency_p99']}ms > 1000ms")
        return alerts
```

## Automated Retraining Pipeline

```yaml
# Retraining workflow
trigger:
  schedule: "0 3 * * 0"         # Weekly retraining
  event:
    - accuracy_drop: < 0.8      # or on accuracy degradation
    - data_drift: psi > 0.25    # or on significant drift
    - manual: true              # or manually triggered

steps:
  - name: Evaluate current model
    action: compute_accuracy(test_data)

  - name: Check if retraining needed
    action: drift_detection(reference, current)

  - name: Retrain model
    action: train(hparams)
    resources:
      gpu: 1
      memory: 32GB

  - name: Evaluate new model
    action: evaluate(test_data)

  - name: Validate performance
    action: |
      if new_accuracy > old_accuracy - 0.01:
        deploy()
      else:
        alert("New model not better, skipping deployment")

  - name: Deploy to staging
    action: deploy_model(staging)

  - name: Shadow traffic test
    action: run_shadow_traffic(1000_requests)

  - name: Promote to production
    action: promote(staging → production)
```

## Shadow Deployment (Traffic Mirroring)

```
Production traffic ──► Production model ──► Response to user
                            │
                            │ (copy request)
                            ▼
                    Shadow model ──► Logged (invisible to user)

Compare shadow predictions vs production for validation.
No user impact if shadow model fails.
```

## Monitoring Infrastructure

```python
# Prometheus metrics for model serving
from prometheus_client import Histogram, Counter, Gauge

PREDICTION_LATENCY = Histogram(
    "model_prediction_latency_seconds",
    "Time per prediction",
    buckets=[0.001, 0.005, 0.01, 0.025, 0.05, 0.1, 0.25, 0.5, 1.0],
)

PREDICTION_COUNTER = Counter(
    "model_predictions_total",
    "Total predictions",
    labelnames=["model", "status"],
)

DRIFT_SCORE = Gauge(
    "model_data_drift_score",
    "Current data drift score",
    labelnames=["model", "feature"],
)

MODEL_ACCURACY = Gauge(
    "model_accuracy",
    "Current model accuracy",
    labelnames=["model"],
)
```

## Alerts

```yaml
# Alert rules
groups:
  - name: model_health
    rules:
      - alert: HighPredictionLatency
        expr: model_prediction_latency_seconds{quantile="0.99"} > 1.0
        for: 5m
        labels:
          severity: warning

      - alert: ModelAccuracyDropped
        expr: model_accuracy < 0.75
        for: 10m
        labels:
          severity: critical

      - alert: FeatureDriftDetected
        expr: model_data_drift_score > 0.25
        for: 30m
        labels:
          severity: warning
```
