---
id: ml-013-0000-0000-0000-000000000062
title: Gradient Boosting
language: markdown
tags: [ai-ml, machine-learning, gradient-boosting, ensemble]
selection: null
isPinned: false
timestamp: 1781900000014
---
# Gradient Boosting

**Links**: [[Ensemble Methods]] | [[Decision Trees and Random Forests]] | [[Hyperparameter Tuning]] | [[Feature Engineering]]

## What is Gradient Boosting?

Gradient boosting builds an ensemble of decision trees sequentially, where each tree corrects the errors of the previous one.

## Popular Libraries

| Library | Speed | GPU | Best For |
|---------|-------|-----|----------|
| **XGBoost** | Fast | Yes | General purpose, competitions |
| **LightGBM** | Fastest | Yes | Large datasets, high cardinality |
| **CatBoost** | Medium | Yes | Categorical features, default params |

## XGBoost

```python
import xgboost as xgb

model = xgb.XGBClassifier(
    n_estimators=1000,
    max_depth=6,
    learning_rate=0.01,
    subsample=0.8,
    colsample_bytree=0.8,
    eval_metric='logloss',
    early_stopping_rounds=50,
)

model.fit(
    X_train, y_train,
    eval_set=[(X_val, y_val)],
    verbose=100,
)
```

## LightGBM

```python
import lightgbm as lgb

model = lgb.LGBMClassifier(
    num_leaves=31,
    learning_rate=0.05,
    n_estimators=1000,
    feature_fraction=0.8,
    bagging_fraction=0.8,
    verbosity=-1,
)

model.fit(
    X_train, y_train,
    eval_set=[(X_val, y_val)],
    callbacks=[lgb.early_stopping(50)],
)
```

## Feature Importance

```python
import matplotlib.pyplot as plt

importance = model.feature_importances_
indices = np.argsort(importance)[-10:]

plt.barh(range(10), importance[indices])
plt.yticks(range(10), [feature_names[i] for i in indices])
```

## Key Hyperparameters

| Parameter | Effect |
|-----------|--------|
| learning_rate | Step size (lower = better but more trees) |
| n_estimators | Number of trees (more = overfit risk) |
| max_depth | Tree depth (deeper = more complex) |
| subsample | Row sampling (prevents overfitting) |
| colsample | Column sampling (prevents overfitting) |
| reg_lambda | L2 regularization |

**Next**: [[Decision Trees and Random Forests]] — Tree-based models