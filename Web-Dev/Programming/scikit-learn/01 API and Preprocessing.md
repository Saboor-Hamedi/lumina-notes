---
tags: [scikit-learn, preprocessing, api, python]
---

# 01 — API and Preprocessing

## Estimator API

Every scikit-learn estimator follows a consistent interface:

```python
estimator.fit(X, y)       # Train model
estimator.predict(X)      # Make predictions
estimator.transform(X)    # Transform data
estimator.score(X, y)     # Evaluate
estimator.get_params()    # Get hyperparameters
estimator.set_params()    # Set hyperparameters
```

## Scaling

| Scaler | Output | Robust to Outliers |
|--------|--------|-------------------|
| `StandardScaler` | Mean=0, std=1 | No |
| `MinMaxScaler` | Range [0, 1] | No |
| `RobustScaler` | Median-based | Yes |
| `MaxAbsScaler` | Range [-1, 1] | Sparse-friendly |

```python
from sklearn.preprocessing import StandardScaler, MinMaxScaler, RobustScaler

scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)
```

## Encoding

```python
from sklearn.preprocessing import OneHotEncoder, LabelEncoder, OrdinalEncoder

encoder = OneHotEncoder(sparse_output=False, handle_unknown='ignore')
encoded = encoder.fit_transform(df[['city', 'state']])
```

- `OneHotEncoder` — creates binary columns per category
- `OrdinalEncoder` — assigns integer labels (0, 1, 2, ...)
- `LabelEncoder` — for target variable encoding

## Text Features

```python
from sklearn.feature_extraction.text import CountVectorizer, TfidfVectorizer

vectorizer = TfidfVectorizer(max_features=5000, stop_words='english', ngram_range=(1, 2))
X_text = vectorizer.fit_transform(texts)
```

## Imputation

```python
from sklearn.impute import SimpleImputer, KNNImputer, IterativeImputer

simp_mean = SimpleImputer(strategy='mean')
simp_median = SimpleImputer(strategy='median')
simp_most_freq = SimpleImputer(strategy='most_frequent')
simp_constant = SimpleImputer(strategy='constant', fill_value=-1)

# KNN imputation (similarity-based)
knn_imputer = KNNImputer(n_neighbors=5, weights='distance')

# MICE-style iterative imputation
iter_imputer = IterativeImputer(
    estimator=RandomForestRegressor(n_estimators=100),
    max_iter=10, random_state=42
)
```

## ColumnTransformer for Mixed Types

```python
from sklearn.compose import ColumnTransformer

numeric_features = ['age', 'salary', 'experience']
categorical_features = ['city', 'department']

preprocessor = ColumnTransformer([
    ('num', StandardScaler(), numeric_features),
    ('cat', OneHotEncoder(handle_unknown='ignore'), categorical_features),
])
```

**Links**: [[Web-Dev/Programming/scikit-learn/02 Feature Engineering]] | [[Web-Dev/Programming/scikit-learn/03 Classification]] | [[Web-Dev/Programming/scikit-learn/09 Pipelines]]
**See also**: [[Python Deep Dive]], [[Pandas for Data Analysis]]
