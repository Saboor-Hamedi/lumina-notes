---
id: a1b2c3d4-1071-4000-8000-000000000071
title: MLOps
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001071
---
# MLOps

MLOps (ML + DevOps) applies DevOps principles to machine learning — automating training, deployment, monitoring, and governance of ML models in production.

## ML Lifecycle

```
Data Collection → Data Validation → Training → Evaluation → Deployment → Monitoring
      ↑                                                            │
      └────────────────────── Retrain (drift) ─────────────────────┘
```

## MLOps vs DevOps

| Aspect | DevOps | MLOps |
|--------|--------|-------|
| Artifact | Code (deterministic) | Model + data (non-deterministic) |
| Testing | Unit/integration tests | Data validation, model evaluation |
| Versioning | Git | Git + DVC (data) + MLflow (models) |
| Drift | N/A | Data drift + concept drift |
| Experimentation | Feature flags | Experiment tracking |
| Reproducibility | Docker + lock files | Data + code + hyperparams + env |

## Key Components

| Component | Purpose | Tools |
|-----------|---------|-------|
| Experiment Tracking | Log params, metrics, artifacts | MLflow, Weights & Biases, Neptune |
| Feature Store | Reusable feature computation | Feast, Tecton |
| Model Registry | Version, stage, deploy models | MLflow, Seldon, BentoML |
| Pipeline Orchestration | Automate training workflows | Airflow, Kubeflow, Prefect |
| Model Serving | Expose model as API | TorchServe, Triton, Seldon |
| Monitoring | Detect drift, performance decay | Evidently, WhyLabs, Arize |

## Experiment Tracking (MLflow)

```python
import mlflow

mlflow.set_experiment("churn-prediction")

with mlflow.start_run():
    # Log parameters
    mlflow.log_param("learning_rate", 0.01)
    mlflow.log_param("n_estimators", 200)

    # Train model
    model = train_model(X_train, y_train)
    accuracy = evaluate_model(model, X_test, y_test)

    # Log metrics
    mlflow.log_metric("accuracy", accuracy)
    mlflow.log_metric("f1_score", f1)

    # Log the model
    mlflow.sklearn.log_model(model, "model")
```

## Feature Store

```python
from feast import FeatureStore

store = FeatureStore(repo_path="./feature_repo")

# Get training features
features = store.get_historical_features(
    entity_df=entity_df,
    features=[
        "user_features:age",
        "user_features:signup_days",
        "transaction_features:avg_transaction_amount",
        "transaction_features:transaction_count_7d"
    ]
).to_df()

# Get online features for serving
online_features = store.get_online_features(
    features=["user_features:age", "transaction_features:transaction_count_7d"],
    entity_rows=[{"user_id": 123}]
).to_dict()
```

## Model Registry (Promote to Production)

```python
# Register model
mlflow.register_model("runs:/<run_id>/model", "ChurnPredictor")

# Transition stage
from mlflow.tracking import MlflowClient
client = MlflowClient()
client.transition_model_version_stage(
    name="ChurnPredictor",
    version=3,
    stage="Production"
)
```

## Serving

| Strategy | Latency | Best For |
|----------|---------|----------|
| Real-time (REST/gRPC) | < 100ms | Online predictions |
| Batch (offline) | Minutes-hours | Bulk scoring |
| Streaming | < 1s | Real-time features |

```python
# BentoML serving
@bentoml.service
class ChurnService:
    model = bentoml.mlflow.get("ChurnPredictor:latest")

    @bentoml.api
    async def predict(self, features: dict) -> dict:
        result = self.model.predict(pd.DataFrame([features]))
        return {"prediction": int(result[0]), "confidence": float(result[1])}
```

## Monitoring

| Signal | What It Detects | Tool |
|--------|----------------|------|
| Data drift | Input distribution change | Evidently, WhyLabs |
| Concept drift | Relationship change (X→Y) | Custom metrics |
| Model decay | Accuracy drops over time | Performance monitoring |
| Prediction drift | Output distribution change | Statistical tests |

```python
# Evidently drift detection
from evidently.report import Report
from evidently.metric_preset import DataDriftPreset

report = Report(metrics=[DataDriftPreset()])
report.run(reference_data=train_df, current_data=new_data)
report.show()
```

## CI/CD for ML

```yaml
# .github/workflows/ml-pipeline.yml
jobs:
  train:
    steps:
      - run: dvc pull  # Get latest data
      - run: python train.py
      - run: mlflow run .
      - run: pytest tests/ -v

  deploy:
    if: github.ref == 'refs/heads/main'
    steps:
      - run: bentoml build
      - run: bentoml containerize ChurnPredictor:latest
      - run: kubectl apply -f deployment.yaml
```

**Links**: [[CI CD Pipelines]] | [[Docker Containers]] | [[Kubernetes Basics]] | [[Model Quantization]] | [[Inference Optimization]] | [[LLMOps and AI Observability]] | [[Model Monitoring in Production]] | [[Feature Stores for Machine Learning]]
