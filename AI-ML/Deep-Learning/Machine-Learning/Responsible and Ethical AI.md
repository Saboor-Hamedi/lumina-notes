---
id: a1b2c3d4-1075-4000-8000-000000000075
title: Responsible and Ethical AI
language: markdown
tags: [ai-ml, machine-learning, responsible-ai, ethics, fairness]
selection: null
isPinned: false
timestamp: 1781500001075
---
# Responsible and Ethical AI

Responsible AI ensures ML systems are fair, transparent, accountable, and respect human rights throughout their lifecycle.

## Core Principles

| Principle | Meaning |
|-----------|---------|
| Fairness | No systematic bias against groups or individuals |
| Transparency | How decisions are made should be explainable |
| Accountability | Clear ownership for model outcomes |
| Privacy | Data collection and use respects user rights |
| Safety | Systems are robust and won't cause harm |
| Human control | Humans can override AI decisions |

## Types of Bias

| Bias Type | Description | Example |
|-----------|-------------|---------|
| Data bias | Training data doesn't represent population | Facial recognition fails on dark skin |
| Label bias | Annotator subjectivity | Sentiment analysis marks AAVE as negative |
| Algorithmic bias | Model amplifies existing patterns | Hiring model penalizes women |
| Deployment bias | Different populations get different service | Credit scoring varies by zip code |
| Measurement bias | Features don't capture the true construct | GPA as proxy for job performance |

## Fairness Definitions

| Definition | Requirement |
|-----------|-------------|
| Demographic parity | Equal acceptance rate across groups |
| Equal opportunity | Equal true positive rate across groups |
| Equalized odds | Equal TPR + equal FPR across groups |
| Individual fairness | Similar individuals get similar predictions |

```python
# Check demographic parity
def check_demographic_parity(predictions, sensitive_attr):
    groups = predictions.groupby(sensitive_attr)
    rates = groups.mean()  # acceptance rate per group
    return rates.max() - rates.min()  # disparity (smaller = more fair)
```

Fairness definitions can conflict — you can't satisfy all simultaneously.

## Explainability Techniques

| Technique | Scope | How |
|-----------|-------|-----|
| LIME | Local (single prediction) | Perturb input, fit linear model |
| SHAP | Local + Global | Game-theoretic feature contributions |
| Feature importance | Global | Permutation or impurity-based |
| Partial dependence | Global | Vary one feature, observe predictions |
| Integrated gradients | Local (deep learning) | Gradient path integral |

```python
import shap

explainer = shap.TreeExplainer(model)
shap_values = explainer.shap_values(X_test)

# Feature importance plot
shap.summary_plot(shap_values, X_test)

# Single prediction explanation
shap.force_plot(explainer.expected_value, shap_values[0], X_test.iloc[0])
```

## Accountability

| Practice | Description |
|----------|-------------|
| Model cards | Document intent, training data, limitations |
| Audit trails | Log every prediction + version + decision |
| Human-in-the-loop | High-stakes decisions require human review |
| Impact assessment | Pre-deployment ethics review |
| Monitoring | Track for drift, bias, performance decay |

## Privacy

| Threat | Mitigation |
|--------|------------|
| Model inversion | Differential privacy training |
| Membership inference | Restrict output granularity |
| Training data extraction | Deduplication, DP-SGD |
| Re-identification | Anonymization, aggregation |

## Regulatory Landscape

| Regulation | Scope | Key Requirements |
|-----------|-------|-----------------|
| EU AI Act | Risk-based regulation | High-risk systems need conformity assessment |
| GDPR | Data privacy | Right to explanation, data minimization |
| CCPA | California consumer privacy | Opt-out of data sale |
| NYC Local Law 144 | Hiring AI | Required bias audit |

## Practical Checklist

Before deploying an ML system:

- [ ] Was training data audited for bias?
- [ ] Can decisions be explained to affected users?
- [ ] Is there a human review process for high-stakes decisions?
- [ ] Are model cards or documentation published?
- [ ] Is performance monitored for drift across population subgroups?
- [ ] Has a fairness-utility tradeoff analysis been done?
- [ ] Is there a process for handling user complaints?
- [ ] Can the system be shut down if safety issues emerge?

## Tradeoffs

```
Fairness vs Accuracy: Debiasing often reduces overall accuracy
Privacy vs Utility: More privacy → less useful data
Transparency vs IP: Open model vs proprietary advantage
Speed vs Caution: Fast deployment vs thorough testing
```

**Links**: [[LLM Alignment]] | [[LLM Safety and Guardrails]] | [[Synthetic Data Generation]] | [[Evaluation of RAG Systems]] | [[Reinforcement Learning]]
