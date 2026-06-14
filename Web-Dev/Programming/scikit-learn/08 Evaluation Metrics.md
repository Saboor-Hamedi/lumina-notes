---
tags: [scikit-learn, evaluation, metrics, python]
---

# 08 — Evaluation Metrics

## Classification Metrics

```python
from sklearn.metrics import (
    accuracy_score, precision_score, recall_score, f1_score,
    confusion_matrix, classification_report,
    roc_auc_score, roc_curve, log_loss
)

print(classification_report(y_test, y_pred))

cm = confusion_matrix(y_test, y_pred)
auc = roc_auc_score(y_test, y_prob)
```

| Metric | When to Use |
|--------|-------------|
| Accuracy | Balanced classes |
| Precision | Minimize false positives (spam, fraud alert) |
| Recall | Minimize false negatives (disease detection) |
| F1-Score | Balance precision and recall |
| ROC AUC | Rank-based, threshold-independent |
| Log Loss | Probabilistic calibration |

## Regression Metrics

```python
from sklearn.metrics import (
    mean_squared_error, mean_absolute_error,
    r2_score, explained_variance_score
)

mse = mean_squared_error(y_test, y_pred)
rmse = mean_squared_error(y_test, y_pred, squared=False)
mae = mean_absolute_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)
```

| Metric | Range | When to Use |
|--------|-------|-------------|
| MSE / RMSE | [0, inf) | Penalize large errors |
| MAE | [0, inf) | Robust to outliers |
| R² | (-inf, 1] | Proportion of variance explained |

## Clustering Metrics

```python
from sklearn.metrics import silhouette_score, adjusted_rand_score

sil_score = silhouette_score(X, labels)
ari = adjusted_rand_score(y_true, labels)
```

**Links**: [[Web-Dev/Programming/scikit-learn/03 Classification]] | [[Web-Dev/Programming/scikit-learn/04 Regression]] | [[Web-Dev/Programming/scikit-learn/05 Clustering]] | [[Web-Dev/Programming/scikit-learn/07 Model Selection]]
**See also**: [[Evaluation of RAG Systems]], [[Model Monitoring in Production]]
