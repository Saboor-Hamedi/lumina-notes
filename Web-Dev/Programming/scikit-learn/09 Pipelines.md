---
tags: [scikit-learn, pipelines, persistence, python]
---

# 09 — Pipelines

## Simple Pipeline

```python
from sklearn.pipeline import Pipeline

pipeline = Pipeline([
    ('scaler', StandardScaler()),
    ('pca', PCA(n_components=10)),
    ('classifier', LogisticRegression()),
])
pipeline.fit(X_train, y_train)
y_pred = pipeline.predict(X_test)
```

## ColumnTransformer Pipeline

```python
from sklearn.compose import ColumnTransformer

numeric_features = ['age', 'salary', 'experience']
categorical_features = ['city', 'department', 'role']

preprocessor = ColumnTransformer([
    ('num', StandardScaler(), numeric_features),
    ('cat', OneHotEncoder(handle_unknown='ignore'), categorical_features),
])

full_pipeline = Pipeline([
    ('preprocessor', preprocessor),
    ('classifier', RandomForestClassifier()),
])
```

## Pipeline with GridSearch

```python
param_grid = {
    'classifier__n_estimators': [100, 200],
    'classifier__max_depth': [5, 10],
    'preprocessor__num__with_mean': [True, False],
}
grid = GridSearchCV(full_pipeline, param_grid, cv=5)
grid.fit(X_train, y_train)
```

The `estimator__param` syntax lets you tune any step in the pipeline.

## Model Persistence

```python
import joblib

# Save
joblib.dump(model, 'model.pkl')
joblib.dump(pipeline, 'pipeline.pkl')

# Load
model = joblib.load('model.pkl')
pipeline = joblib.load('pipeline.pkl')
```

## Decision Guide

| Problem Type | First Try | Tune Toward | If Overfitting |
|-------------|-----------|-------------|----------------|
| Binary classification | LogisticRegression | GradientBoosting, RF | Increase regularization |
| Multi-class | RandomForest | HistGradientBoosting | Reduce max_depth |
| Regression | Ridge | GradientBoostingRegressor | Increase min_samples_leaf |
| High-dimensional | LinearSVM, Lasso | SelectKBest + any model | Increase penalty |
| Text data | TfidfVectorizer + LinearSVM | Transformers | ngram_range, max_features |
| Large dataset | SGDClassifier | HistGradientBoosting | Early stopping |
| Imbalanced | class_weight='balanced' | SMOTE + RF | Increase class weight |
| Time series | TimeSeriesSplit + LightGBM | Prophet | Strict temporal CV |

**Links**: [[Web-Dev/Programming/scikit-learn/01 API and Preprocessing]] | [[Web-Dev/Programming/scikit-learn/03 Classification]] | [[Web-Dev/Programming/scikit-learn/07 Model Selection]] | [[Web-Dev/Programming/scikit-learn/08 Evaluation Metrics]]
**See also**: [[MLOps]], [[CI CD Pipelines]], [[Model Monitoring in Production]]
