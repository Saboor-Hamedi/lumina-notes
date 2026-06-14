---
tags: [scikit-learn, regression, python]
---

# 04 — Regression

## Available Regressors

| Model | Regularization | When to Use |
|-------|----------------|-------------|
| `LinearRegression` | None | Baseline, OLS assumptions met |
| `Ridge` | L2 | Multicollinearity, shrink coefficients |
| `Lasso` | L1 | Feature selection, sparse solutions |
| `ElasticNet` | L1 + L2 | Many features, groups of correlated |
| `SVR` | Kernel | Non-linear, small datasets |
| `DecisionTreeRegressor` | None | Non-linear, interpretable |
| `RandomForestRegressor` | None | Medium data, good defaults |
| `GradientBoostingRegressor` | None | Structured data, best accuracy |

## Code Examples

```python
from sklearn.linear_model import LinearRegression, Ridge, Lasso, ElasticNet
from sklearn.ensemble import RandomForestRegressor, GradientBoostingRegressor

# Linear with regularization
model = Ridge(alpha=1.0)                           # L2
model = Lasso(alpha=0.1)                           # L1 (feature selection)
model = ElasticNet(alpha=0.1, l1_ratio=0.5)        # L1 + L2

# Non-linear
model = RandomForestRegressor(n_estimators=200, max_depth=15)
model = GradientBoostingRegressor(n_estimators=300, learning_rate=0.05)
```

## Evaluation

```python
from sklearn.metrics import mean_squared_error, mean_absolute_error, r2_score

mse = mean_squared_error(y_test, y_pred)
rmse = mean_squared_error(y_test, y_pred, squared=False)
mae = mean_absolute_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)
```

**Links**: [[Web-Dev/Programming/scikit-learn/03 Classification]] | [[Web-Dev/Programming/scikit-learn/08 Evaluation Metrics]] | [[Web-Dev/Programming/scikit-learn/07 Model Selection]]
**See also**: [[Ensemble Methods]], [[Hyperparameter Tuning]]
