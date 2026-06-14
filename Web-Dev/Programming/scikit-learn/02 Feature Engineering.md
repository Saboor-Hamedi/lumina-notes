---
tags: [scikit-learn, feature-engineering, python]
---

# 02 — Feature Engineering

## Polynomial Features

```python
from sklearn.preprocessing import PolynomialFeatures

poly = PolynomialFeatures(degree=2, interaction_only=False, include_bias=True)
X_poly = poly.fit_transform(X)
print(poly.get_feature_names_out())
```

## Spline Transformation

```python
from sklearn.preprocessing import SplineTransformer

spline = SplineTransformer(n_knots=5, degree=3, extrapolation='constant')
X_spline = spline.fit_transform(X[['age']])
```

## Discretization

```python
from sklearn.preprocessing import KBinsDiscretizer

disc = KBinsDiscretizer(n_bins=5, encode='ordinal', strategy='quantile')
X_binned = disc.fit_transform(X[['age']])
```

| Strategy | Behavior |
|----------|----------|
| `uniform` | Equal-width bins |
| `quantile` | Equal-frequency bins |
| `kmeans` | Bin edges from k-means clusters |

## Power and Quantile Transforms

```python
from sklearn.preprocessing import PowerTransformer, QuantileTransformer

# Makes data more Gaussian
pt = PowerTransformer(method='yeo-johnson')

# Maps to uniform/normal distribution
qt = QuantileTransformer(n_quantiles=1000, output_distribution='normal')
```

## Custom Transformers

```python
from sklearn.preprocessing import FunctionTransformer
import numpy as np

def add_interaction(X):
    if X.shape[1] >= 2:
        interaction = X[:, 0:1] * X[:, 1:2]
        return np.hstack([X, interaction])
    return X

ft = FunctionTransformer(add_interaction, validate=True)
X_with_interaction = ft.fit_transform(X)
```

## Feature Selection

| Method | When to Use |
|--------|-------------|
| `VarianceThreshold` | Remove constant / near-constant features |
| `SelectKBest` | Univariate selection (chi2, f_classif, mutual_info) |
| `RFE` / `RFECV` | Recursive elimination, auto-select k via CV |
| `SelectFromModel` | Importance-based from any estimator |
| `SequentialFeatureSelector` | Forward/backward search |

```python
from sklearn.feature_selection import SelectFromModel, RFECV

selector = SelectFromModel(
    estimator=RandomForestClassifier(n_estimators=200),
    threshold='mean', max_features=20
)

rfecv = RFECV(
    estimator=LogisticRegression(max_iter=1000),
    step=1, cv=5, scoring='f1'
)
rfecv.fit(X, y)
print(f"Optimal features: {rfecv.n_features_}")
```

**Links**: [[Web-Dev/Programming/scikit-learn/01 API and Preprocessing]] | [[Web-Dev/Programming/scikit-learn/03 Classification]] | [[Web-Dev/Programming/scikit-learn/06 Dimensionality Reduction]]
**See also**: [[Dimensionality Reduction]], [[Data Normalization Rules]]
