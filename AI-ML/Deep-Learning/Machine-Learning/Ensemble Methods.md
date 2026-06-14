---
id: ml-012-0000-0000-0000-000000000061
title: Ensemble Methods
language: markdown
tags: [ai-ml, machine-learning, ensemble]
selection: null
isPinned: false
timestamp: 1781900000013
---
# Ensemble Methods

**Links**: [[Decision Trees and Random Forests]] | [[Gradient Boosting]] | [[Hyperparameter Tuning]] | [[Text Classification]] | [[Feature Engineering]]

## What are Ensembles?

Ensemble methods combine multiple models to produce better results than any single model.

## Bagging (Bootstrap Aggregating)

Train models in parallel on random subsets of data.

```
Data → Bootstrap 1 → Model 1 ──┐
     → Bootstrap 2 → Model 2 ──┤→ Average/Vote → Prediction
     → Bootstrap 3 → Model 3 ──┘
```

- **Random Forest**: Bagging + random feature selection
- Reduces **variance** (overfitting)

## Boosting

Train models sequentially, each correcting the previous.

```
Data → Model 1 (weights) → Model 2 (corrects errors) → Model 3 → ...
```

- **AdaBoost**, **Gradient Boosting**, **XGBoost**
- Reduces **bias** (underfitting)

## Stacking (Stacked Generalization)

Train a meta-model on predictions of base models.

```python
from sklearn.ensemble import StackingClassifier
from sklearn.linear_model import LogisticRegression

stack = StackingClassifier(
    estimators=[
        ('rf', RandomForestClassifier()),
        ('svm', SVC(probability=True)),
        ('gb', GradientBoostingClassifier()),
    ],
    final_estimator=LogisticRegression()
)
```

## Voting

```python
from sklearn.ensemble import VotingClassifier

voting = VotingClassifier(
    estimators=[
        ('lr', LogisticRegression()),
        ('rf', RandomForestClassifier()),
        ('svm', SVC()),
    ],
    voting='soft'  # Weighted probabilities
)
```

## When to Use

| Method | When |
|--------|------|
| Bagging | High variance, overfitting |
| Boosting | High bias, underfitting |
| Stacking | Very different model types |
| Voting | Need robustness, lower risk |

**Next**: [[Gradient Boosting]] — XGBoost, LightGBM, CatBoost