---
id: ml-017-0000-0000-0000-000000000066
title: Recommender Systems
language: markdown
tags:
  - ai-ml
  - machine-learning
  - recommender-systems
selection: null
isPinned: false
customIcon: null
timestamp: 1781882019319
---
# Recommender Systems

**Links**: [[Clustering Algorithms]] | [[Dimensionality Reduction]] | [[Word Embeddings]] | [[Vector Databases for RAG]] | [[Retrieval Strategies]] | [[Redis Deep Dive]] | [[Apache Kafka Deep Dive]] | [[Feature Stores for Machine Learning]]

**See also**: [[MLOps]], [[System-Design/Architecture/Event-Driven Architecture]], [[DevOps/Monitoring/Monitoring and Observability]]

## Types of Recommender Systems

| Type | How It Works | Example |
|------|-------------|---------|
| **Content-Based** | Recommend similar items to what user liked | Same genre movies |
| **Collaborative Filtering** | Users with similar tastes → similar recommendations | "Users who bought X also bought Y" |
| **Hybrid** | Combine both approaches | Netflix, Amazon |

## Content-Based Filtering

```python
from sklearn.metrics.pairwise import cosine_similarity

# Item features → compute similarity matrix
item_vectors = tfidf_matrix  # TF-IDF of item descriptions
similarity = cosine_similarity(item_vectors)

# Recommend most similar items
def recommend(item_id, n=5):
    scores = list(enumerate(similarity[item_id]))
    scores = sorted(scores, key=lambda x: x[1], reverse=True)
    return scores[1:n+1]  # Skip self
```

## Collaborative Filtering (Matrix Factorization)

```python
from surprise import SVD, Dataset

# User-item rating matrix
data = Dataset.load_from_df(ratings[['user', 'item', 'rating']])

model = SVD(n_factors=100, reg_all=0.02)
model.fit(data.build_full_trainset())

# Predict rating for user-item pair
pred = model.predict(user_id, item_id)
```

## Cold Start Problem

| Challenge | Solution |
|-----------|----------|
| New user, no history | Ask preferences, use demographics |
| New item, no ratings | Use content-based features |
| New system, no data | Start with popularity-based |

## Evaluation

```python
# Train/test split by time (not random!)
train = ratings[ratings['date'] < '2024-06-01']
test = ratings[ratings['date'] >= '2024-06-01']

# Metrics
from sklearn.metrics import mean_absolute_error, mean_squared_error

mae = mean_absolute_error(test['rating'], predictions)
rmse = mean_squared_error(test['rating'], predictions, squared=False)
```

**Next**: [[Clustering Algorithms]] — Grouping unlabeled data
