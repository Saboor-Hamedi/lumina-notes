---
tags: [scikit-learn, classification, python]
---

# 03 — Classification

## Available Classifiers

| Model | Type | When to Use |
|-------|------|-------------|
| `LogisticRegression` | Linear | Baseline, fast, interpretable |
| `LinearSVC` | Linear | High-dimensional, text data |
| `SVC` | Kernel | Non-linear boundaries, small datasets |
| `DecisionTreeClassifier` | Tree | Interpretable, needs pruning |
| `RandomForestClassifier` | Ensemble | Medium data, good defaults |
| `GradientBoostingClassifier` | Ensemble | Structured data, best accuracy |
| `KNeighborsClassifier` | Instance-based | Non-parametric, small data |
| `GaussianNB` | Probabilistic | Very fast, works with high dim |
| `HistGradientBoostingClassifier` | Ensemble | Large datasets (n > 10K) |

## Code Examples

```python
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC
from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier

# Logistic Regression
model = LogisticRegression(C=1.0, solver='lbfgs', max_iter=1000)
model.fit(X_train, y_train)
y_pred = model.predict(X_test)
y_prob = model.predict_proba(X_test)

# SVM with RBF kernel
model = SVC(kernel='rbf', C=1.0, gamma='scale', probability=True)

# Random Forest
model = RandomForestClassifier(
    n_estimators=200, max_depth=10,
    min_samples_split=5, class_weight='balanced'
)
```

## Handling Imbalanced Data

```python
# Built-in class weighting
model = RandomForestClassifier(class_weight='balanced')
model = LogisticRegression(class_weight='balanced')

# Or use imbalanced-learn (separate library)
from imblearn.over_sampling import SMOTE
smote = SMOTE(random_state=42)
X_resampled, y_resampled = smote.fit_resample(X_train, y_train)
```

**Links**: [[Web-Dev/Programming/scikit-learn/04 Regression]] | [[Web-Dev/Programming/scikit-learn/05 Clustering]] | [[Web-Dev/Programming/scikit-learn/08 Evaluation Metrics]]
**See also**: [[Decision Trees and Random Forests]], [[Gradient Boosting]], [[Hyperparameter Tuning]]
