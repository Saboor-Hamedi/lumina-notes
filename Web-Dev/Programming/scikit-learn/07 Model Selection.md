---
tags: [scikit-learn, model-selection, cross-validation, hyperparameter-tuning, python]
---

# 07 — Model Selection

## Train/Test Split

```python
from sklearn.model_selection import train_test_split, GroupShuffleSplit

# Standard random split
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Stratified split (preserve class proportions)
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

# Group split (keep groups together — no leakage)
group_split = GroupShuffleSplit(n_splits=1, test_size=0.2, random_state=42)
train_idx, test_idx = next(group_split.split(X, y, groups=df['customer_id']))
```

## Cross-Validation Strategies

| Strategy | Use Case |
|----------|----------|
| `KFold` | General purpose |
| `StratifiedKFold` | Imbalanced classification |
| `GroupKFold` | Groups must not split (per-patient) |
| `RepeatedKFold` | More reliable variance estimate |
| `TimeSeriesSplit` | Temporal data (expanding window) |
| `LeaveOneOut` | Very small datasets |

```python
from sklearn.model_selection import StratifiedKFold, cross_validate

cv_results = cross_validate(
    model, X, y,
    cv=StratifiedKFold(5),
    scoring={'accuracy': 'accuracy', 'f1': 'f1_macro'},
    return_train_score=True,
)
print(f"F1: {cv_results['test_f1'].mean():.3f} +/- {cv_results['test_f1'].std():.3f}")
```

## Hyperparameter Tuning

```python
from sklearn.model_selection import GridSearchCV, RandomizedSearchCV

param_grid = {
    'n_estimators': [100, 200, 300],
    'max_depth': [5, 10, 15, None],
    'min_samples_split': [2, 5, 10],
}

grid_search = GridSearchCV(
    RandomForestClassifier(), param_grid,
    cv=5, scoring='f1', n_jobs=-1, verbose=1
)
grid_search.fit(X_train, y_train)
print(f"Best params: {grid_search.best_params_}")
print(f"Best CV score: {grid_search.best_score_:.3f}")

# Randomized search — faster for large spaces
random_search = RandomizedSearchCV(
    model, param_distributions, n_iter=50, cv=5, random_state=42
)
```

**Links**: [[Web-Dev/Programming/scikit-learn/08 Evaluation Metrics]] | [[Web-Dev/Programming/scikit-learn/09 Pipelines]] | [[Web-Dev/Programming/scikit-learn/03 Classification]]
**See also**: [[Hyperparameter Tuning]], [[Cross-Validation Strategies]]
