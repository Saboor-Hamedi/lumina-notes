---
id: a1b2c3d4-1121-4000-8000-000000000121
title: Causal Inference
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001121
---
# Causal Inference

Causal inference moves beyond correlation to determine cause-and-effect relationships. It's essential for A/B testing, policy evaluation, and decision-making.

## Correlation != Causation

```
Correlation: Ice cream sales ↑ and drowning ↑ (both caused by summer heat)
Causation: Drug → Recovery (drug causes the recovery)
```

| Question | Statistics | Causality |
|----------|-----------|-----------|
| "Is X related to Y?" | Correlation | — |
| "Does X cause Y?" | — | Causal inference |
| "What if we changed X?" | — | Counterfactual reasoning |

## Causal DAGs (Directed Acyclic Graphs)

```
Treatment → Outcome
    ↑
Confounder (bias)
```

```python
# Example: Does education cause higher income?
# Confounder: Ability (affects both education and income)

# DAG: Education → Income
# DAG: Ability → Education, Ability → Income
```

## Confounders

A confounder affects both treatment and outcome, creating spurious correlation:

```
Confounder: Age
    ↓        ↘
Treatment → Outcome
```

**Examples**:
- Ice cream → Drowning (confounded by Summer) ❌
- Exercise → Health (confounded by Age) ⚠️

## Adjustment Methods

### 1. Backdoor Adjustment

```python
# Block backdoor paths by conditioning on confounders
# Effect of Treatment on Outcome, controlling for Confounders
import statsmodels.api as sm

# Linear regression with controls
X = df[["treatment", "age", "income", "education"]]  # add confounders
X = sm.add_constant(X)
model = sm.OLS(df["outcome"], X).fit()
treatment_effect = model.params["treatment"]
```

### 2. Propensity Score Matching

```python
from sklearn.linear_model import LogisticRegression
import numpy as np

# Step 1: Estimate propensity scores (P(treatment | confounders))
ps_model = LogisticRegression()
ps_model.fit(X_confounders, df["treatment"])
propensity = ps_model.predict_proba(X_confounders)[:, 1]

# Step 2: Match treated vs untreated with similar propensity
# (Nearest neighbor, caliper matching, etc.)
```

### 3. Difference-in-Differences (DiD)

Compare treatment group before/after vs control group before/after:

```python
# DiD estimator
effect = (treatment_after - treatment_before) - (control_after - control_before)
```

### 4. Instrumental Variables (IV)

When confounders are unobserved, use an instrument Z that:
- Affects treatment (relevance)
- Only affects outcome through treatment (exclusion)
- Not correlated with confounders (exogeneity)

## A/B Testing (RCT)

Randomized Controlled Trials are the gold standard — randomization breaks the link between confounders and treatment.

```python
from scipy.stats import ttest_ind

# Random assignment → no confounders (on average)
control = df[df["group"] == "control"]["conversion"]
treatment = df[df["group"] == "treatment"]["conversion"]

stat, p_value = ttest_ind(control, treatment)
significant = p_value < 0.05

# Effect size
effect = treatment.mean() - control.mean()
```

| Tool | Purpose |
|------|---------|
| scipy.stats | T-tests, chi-squared |
| statsmodels | Regression, DiD, matching |
| DoWhy (Microsoft) | Causal graph + estimation |
| CausalNex | Bayesian causal networks |
| EconML (Microsoft) | Heterogeneous treatment effects |

## Do-Calculus (Pearl)

Formal framework for causal reasoning:

| Operation | Meaning |
|-----------|---------|
| P(Y | do(X)) | Probability of Y if we SET X |
| P(Y | X) | Probability of Y GIVEN we observe X |
| P(Y | do(X), Z) | Effect of X on Y, controlling for Z |

## Simpson's Paradox

When a trend appears in subgroups but reverses when groups are combined:

```
Overall: Treatment is less effective (48% vs 52%)
By subgroup: Treatment is more effective for ALL subgroups!
```

**Cause**: Confounder (e.g., kidney stone size) — treatment given to more severe cases.

## When to Use Which Method

| Method | When | Assumptions |
|--------|------|-------------|
| RCT / A/B test | Feasible to randomize | Randomization worked |
| Regression | Measured confounders | No unmeasured confounders |
| Matching | Overlap between groups | Sufficiently rich confounders |
| DiD | Pre/post data for both groups | Parallel trends |
| IV | Unmeasured confounders, valid instrument | Valid instrument |

**Links**: [[A/B Testing]] | [[Bayesian Methods]] | [[Machine Learning]] | [[Causal ML]] | [[Statistical Methods]]
