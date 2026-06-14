---
id: ml-011-0000-0000-0000-000000000060
title: Hyperparameter Tuning
language: markdown
tags: [ai-ml, machine-learning, hyperparameters, optimization]
selection: null
isPinned: false
timestamp: 1781900000012
---
# Hyperparameter Tuning

**Links**: [[Feature Engineering]] | [[Ensemble Methods]] | [[Gradient Boosting]] | [[Decision Trees and Random Forests]] | [[Pre-training and Fine-tuning]]

## What is Hyperparameter Tuning?

Hyperparameters are configuration settings not learned during training. Tuning finds the best combination for optimal performance.

## Methods

| Method | Description | Iterations | Best For |
|--------|-------------|------------|----------|
| **Manual** | Guess and check | Few | Simple models |
| **Grid Search** | Try all combinations | Many | Small search space |
| **Random Search** | Sample random combos | Medium | Most cases |
| **Bayesian** | Model-based optimization | Medium | Expensive evaluations |
| **Optuna** | Automated pruning | Efficient | General purpose |

## Grid Search

```python
from sklearn.model_selection import GridSearchCV

param_grid = {
    'n_estimators': [100, 200, 300],
    'max_depth': [5, 10, None],
    'min_samples_split': [2, 5, 10],
}

grid = GridSearchCV(
    RandomForestClassifier(),
    param_grid,
    cv=5,
    scoring='f1',
    n_jobs=-1,
    verbose=1
)
grid.fit(X_train, y_train)
print(grid.best_params_)
```

## Random Search

```python
from sklearn.model_selection import RandomizedSearchCV

param_dist = {
    'n_estimators': randint(50, 500),
    'max_depth': [5, 10, 20, 30, None],
    'learning_rate': uniform(0.01, 0.3),
}
```

## Optuna

```python
import optuna

def objective(trial):
    params = {
        'lr': trial.suggest_float('lr', 1e-5, 1e-1, log=True),
        'layers': trial.suggest_int('layers', 1, 5),
        'dropout': trial.suggest_float('dropout', 0.1, 0.5),
    }
    return train_model(params).accuracy

study = optuna.create_study(direction='maximize')
study.optimize(objective, n_trials=100)
```

**Next**: [[Ensemble Methods]] — Combining models for better results