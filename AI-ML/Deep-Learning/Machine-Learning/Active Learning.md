---
id: dev-060-0000-0000-0000-000000000046
title: Active Learning
language: markdown
tags: [ai-ml, machine-learning, active-learning]
selection: null
isPinned: false
timestamp: 1781800000024
---
# Active Learning

**Links**: [[Data Augmentation for NLP]] | [[Text Classification]] | [[Named Entity Recognition]] | [[Pre-training and Fine-tuning]] | [[Evaluation of RAG Systems]]

## What is Active Learning?

Active learning selects the most informative unlabeled examples for human labeling, maximizing model accuracy with minimal labeled data.

## The Loop

```
Unlabeled Data Pool
    │
    ▼
┌──────────────────┐
│ Select samples    │ ← Query strategy
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Human annotates   │  ← Most uncertain / diverse samples
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Retrain model     │  ← Updated with new labels
└────────┬─────────┘
         │
         ▼
    Repeat
```

## Query Strategies

| Strategy | Description |
|----------|-------------|
| **Uncertainty Sampling** | Pick examples where model is least confident |
| **Margin Sampling** | Pick with smallest margin between top 2 predictions |
| **Entropy Sampling** | Pick with highest prediction entropy |
| **Diversity Sampling** | Pick most representative examples |
| **Query-by-Committee** | Train multiple models, pick where they disagree |

## Uncertainty Sampling

```python
def uncertainty_sampling(model, unlabeled, n=10):
    probs = model.predict_proba(unlabeled)
    # Lowest max probability = most uncertain
    uncertainty = 1 - probs.max(axis=1)
    indices = np.argsort(uncertainty)[-n:]
    return unlabeled[indices]
```

## Benefits

- **80% less labeling**: Achieves same accuracy with fewer labels
- **Smart labeling**: Focus on edge cases, not obvious examples
- **Iterative improvement**: Model improves where it's weakest

**Next**: [[Data Augmentation for NLP]] — Create synthetic training data