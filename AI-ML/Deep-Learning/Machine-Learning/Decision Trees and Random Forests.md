---
id: ml-014-0000-0000-0000-000000000063
title: Decision Trees and Random Forests
language: markdown
tags: [ai-ml, machine-learning, decision-trees, random-forest, ensemble]
selection: null
isPinned: false
timestamp: 1781900000015
---
# Decision Trees and Random Forests

**Links**: [[Ensemble Methods]] | [[Gradient Boosting]] | [[Feature Engineering]] | [[Hyperparameter Tuning]]

## Decision Trees

A decision tree splits data into branches based on feature values, creating if-then-else rules.

```
                [Outlook]
              /    |     \
          Sunny  Overcast  Rain
           /        |        \
      [Humidity]  [Yes]    [Wind]
       /    \              /    \
    High   Normal       Strong  Weak
     /        \          /       \
   [No]     [Yes]      [No]    [Yes]
```

## How Splits Are Chosen

```python
from sklearn.tree import DecisionTreeClassifier

tree = DecisionTreeClassifier(
    max_depth=5,
    min_samples_split=10,
    criterion='gini'  # or 'entropy'
)
tree.fit(X_train, y_train)
```

- **Gini impurity**: Measures class imbalance in a node
- **Information gain**: Reduction in entropy after split

## Overfitting

Decision trees easily overfit. Control with:

```python
tree = DecisionTreeClassifier(
    max_depth=5,              # Limit tree depth
    min_samples_split=20,     # Min samples to split
    min_samples_leaf=5,       # Min samples per leaf
    max_features='sqrt',      # Features per split
    ccp_alpha=0.01,           # Pruning parameter
)
```

## Random Forest

Random forest trains many trees on random subsets and averages their predictions.

```python
from sklearn.ensemble import RandomForestClassifier

rf = RandomForestClassifier(
    n_estimators=500,
    max_depth=10,
    min_samples_split=5,
    n_jobs=-1,           # Parallel training
    random_state=42,
)
rf.fit(X_train, y_train)
```

## Pros and Cons

| Pros | Cons |
|------|------|
| No feature scaling needed | Can overfit (without tuning) |
| Handles mixed data types | Less interpretable than single tree |
| Feature importance built-in | Slower with many trees |
| Resistant to outliers | Poor at extrapolation |
| Parallel training | Large model size |

**Next**: [[Gradient Boosting]] — XGBoost, LightGBM, CatBoost