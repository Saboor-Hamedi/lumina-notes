---
id: a1b2c3d4-1174-4000-8000-000000000174
title: Feature Stores for Machine Learning
language: markdown
tags:
  - ai-ml
  - machine-learning
  - feature-store
  - ml-engineering
selection: null
isPinned: false
timestamp: 1781831042739
---
# Feature Stores for Machine Learning

A feature store is a centralized repository for managing, storing, and serving machine learning features consistently across training and inference.

## Why Feature Stores

```
Without feature store:
  Training:    data_scientist_a/features/user_features.py
               data_scientist_b/features/user_features.py  (copy-paste, slightly different!)
  Inference:   model_server/compute_features.py  (different implementation!)

  → Training/serving skew
  → Duplicate work
  → No feature sharing
  → No consistency

With feature store:
  training_features = feature_store.get_features("user_features", version=3)
  inference_features = feature_store.get_features("user_features", version=3)
  
  → Same code, same values, consistent
```

## Architecture

```
Offline Store (batch)
  ┌──────────────┐
  │ Data Lake    │────► Feature Engineering ────► Feature Store
  │ Warehouse    │                                    │
  └──────────────┘                               ┌────┴────┐
  Historical features for training               │ Online  │
                                                 │ Store   │
Online Store (real-time)                         │(Redis,  │
  ┌──────────────┐                               │DynamoDB)│
  │ Stream       │────► Feature Computation ────►└─────────┘
  │ (Kafka)      │                                    │
  └──────────────┘                                    │
                                         Serving ─────┴────
                                         Training  │ Inference
```

## Key Components

| Component | Description | Technology |
|-----------|-------------|------------|
| Offline Store | Historical features for training | Parquet, Delta Lake |
| Online Store | Low-latency feature serving | Redis, DynamoDB |
| Feature Registry | Metadata catalog | PostgreSQL |
| Transformation Engine | Compute features | Spark, Flink |
| Serving API | Get features for inference | REST, gRPC |

## Example: Feast

```python
from feast import FeatureStore, Entity, FeatureView, Field
from feast.types import Float32, Int64, String
from datetime import timedelta

# Define entity
customer = Entity(name="customer_id", description="Customer identifier")

# Define features
customer_features = FeatureView(
    name="customer_features",
    entities=["customer_id"],
    ttl=timedelta(days=2),
    schema=[
        Field(name="total_purchases", dtype=Int64),
        Field(name="avg_order_value", dtype=Float32),
        Field(name="customer_tier", dtype=String),
        Field(name="days_since_last_purchase", dtype=Int64),
    ],
    source=("batch", "s3://data/features/customer/*.parquet"),
)

# Initialize store
store = FeatureStore(repo_path="./feature_repo")

# Get training data (offline)
training_df = store.get_historical_features(
    entity_df=entity_df,  # DataFrame with customer_id and timestamps
    features=[
        "customer_features:total_purchases",
        "customer_features:avg_order_value",
        "customer_features:customer_tier",
    ],
).to_df()

# Materialize to online store
store.materialize_incremental(end_date=datetime.now())

# Get features for inference (online)
features = store.get_online_features(
    features=["customer_features:total_purchases", "customer_features:avg_order_value"],
    entity_rows=[{"customer_id": "123"}, {"customer_id": "456"}],
).to_dict()
```

## Feature Definition

```yaml
# feature_definition.yaml
features:
  - name: user_purchase_stats
    type: batch
    schedule: daily
    source: |
      SELECT
        user_id,
        COUNT(*) as purchase_count_7d,
        AVG(amount) as avg_purchase_7d,
        SUM(amount) as total_spent_7d,
        MAX(amount) as max_purchase_7d
      FROM orders
      WHERE order_date >= CURRENT_DATE - 7
      GROUP BY user_id
    owner: data-science-team
    freshness: 1h
    tags: [purchase, user]

  - name: user_page_views_1h
    type: streaming
    source: kafka_page_views
    window: 1h
    sql: |
      SELECT
        user_id,
        COUNT(*) as page_views_1h,
        COUNT(DISTINCT page) as unique_pages_1h
      FROM page_views_stream
      GROUP BY TUMBLE(event_time, INTERVAL '1' HOUR), user_id
    serving: redis
    ttl: 2h
```

## Training-Serving Skew Prevention

```python
# Same compute logic for training and serving
class ComputeFeatures:
    def __init__(self):
        self.feature_store = FeastFeatureStore()

    def compute_features(self, df):
        return df.groupby("user_id").agg({
            "amount": ["count", "mean", "sum"],
            "event_timestamp": "max",
        }).rename(columns={
            ("amount", "count"): "purchase_count",
            ("amount", "mean"): "avg_purchase",
            ("amount", "sum"): "total_purchases",
            ("event_timestamp", "max"): "last_purchase",
        })

    def get_training_data(self, start_date, end_date):
        raw = self._query_raw(start_date, end_date)
        return self.compute_features(raw)

    def get_online_features(self, user_id):
        raw = self._query_recent(user_id)
        return self.compute_features(raw)
```

## Point-in-Time Correctness

```python
# Avoid data leakage — use features as they existed at prediction time
training_data = feature_store.get_historical_features(
    entity_df=entity_df,  # Contains: user_id, label_date (when prediction was made)
    features=["user_features:purchase_count_7d", "user_features:avg_order_value"],
    full_feature_names=True,
)
# Feast automatically joins features as they existed at label_date
```

## Feature Serving Patterns

| Pattern | Latency | Use Case |
|---------|---------|----------|
| Pre-computed (batch) | Seconds | Stable features, daily updates |
| Streaming | Milliseconds | Real-time features (page views) |
| On-demand | Milliseconds | Features requiring request data |
| Embedding | Variable | NLP features |

## Popular Feature Stores

| Platform | Open Source | Offline | Online |
|----------|-------------|---------|--------|
| Feast | Yes | Spark, BigQuery, Snowflake | Redis, DynamoDB |
| Tecton | No | Databricks, Snowflake | DynamoDB, Redis |
| SageMaker Feature Store | No | S3 | DynamoDB |
| Vertex AI Feature Store | No | GCS | Bigtable |
| Hopsworks | Partially | HopsFS, Hive | MySQL Cluster |

**Links**: [[Active Learning]] | [[Anomaly Detection]] | [[Causal Inference]] | [[Clustering Algorithms]] | [[Data Augmentation for NLP]] | [[Decision Trees and Random Forests]] | [[Dimensionality Reduction]] | [[Ensemble Methods]] | [[Gradient Boosting]] | [[Graph Neural Networks]] | [[Hyperparameter Tuning]] | [[Information Theory]] | [[MLOps]] | [[Model Monitoring in Production]] | [[Neural Architecture Search]] | [[Recommender Systems]] | [[Reinforcement Learning]] | [[Responsible and Ethical AI]] | [[Synthetic Data Generation]] | [[Time Series Analysis]]
