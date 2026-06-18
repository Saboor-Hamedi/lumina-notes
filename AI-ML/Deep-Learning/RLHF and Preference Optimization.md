---
id: new-ai-022-0000-0000-0000-000000000022
title: RLHF and Preference Optimization
language: markdown
tags:
  - deep-learning
  - nlp
  - alignment
selection: null
isPinned: false
timestamp: 1781796134931
---

# RLHF and Preference Optimization

**Links**: [[LLM Alignment]] | [[Pre-training and Fine-tuning]] | [[LoRA and Parameter-Efficient Fine-Tuning]] | [[LLM Safety and Guardrails]] | [[LLM Evaluation and Benchmarks]] | [[Responsible and Ethical AI]]

**See also**: [[Transformer Architecture]], [[Inference Optimization]], [[Advanced Prompting Techniques]]

## Overview

Reinforcement Learning from Human Feedback (RLHF) aligns LLMs with human preferences. It transforms a raw pretrained model into a helpful, harmless, honest assistant.

## The RLHF Pipeline

```
Pretrained Model
      ↓
SFT (Supervised Fine-Tuning) — learn from human demonstrations
      ↓
Reward Model Training — learn what humans prefer
      ↓
RL Fine-Tuning (PPO) — optimize policy against reward model
      ↓
Aligned Model
```

## Step 1: Supervised Fine-Tuning (SFT)

Train on human-written demonstrations:

```python
# Standard cross-entropy on human-preferred completions
loss = cross_entropy(model_output, human_demonstration)
```

## Step 2: Reward Modeling

Train a model to predict human preference:

```python
# Bradley-Terry preference model
# P(y_i > y_j | x) = σ(r(x, y_i) - r(x, y_j))
import torch.nn as nn

class RewardModel(nn.Module):
    def __init__(self, base_model):
        super().__init__()
        self.base = base_model
        self.reward_head = nn.Linear(hidden_size, 1)

    def forward(self, x, y):
        hidden = self.base(x, y)
        return self.reward_head(hidden).squeeze(-1)

# Loss: prefer chosen over rejected
loss = -log_sigmoid(r_chosen - r_rejected)
```

## Step 3: PPO (Proximal Policy Optimization)

```python
# Simplified PPO for LLMs
for _ in range(ppo_epochs):
    # Generate completion from current policy
    completion = policy.generate(prompt)

    # Get reward from reward model
    reward = reward_model(prompt, completion)

    # KL penalty to prevent diverging too far
    kl_div = kl_divergence(policy(completion), ref_policy(completion))

    # PPO objective
    ratio = exp(log_prob_new - log_prob_old)
    clipped_ratio = clamp(ratio, 1-ε, 1+ε)
    ppo_loss = -min(ratio * advantage, clipped_ratio * advantage)
    total_loss = ppo_loss + β * kl_div
```

## Alternative: DPO (Direct Preference Optimization)

DPO eliminates the need for a separate reward model:

```python
# DPO loss: directly optimize policy from preferences
def dpo_loss(policy_logps, ref_logps, prefered, rejected, β):
    prefered_diff = policy_logps[prefered] - ref_logps[prefered]
    rejected_diff = policy_logps[rejected] - ref_logps[rejected]
    logits = prefered_diff - rejected_diff
    return -log_sigmoid(β * logits).mean()
```

## Comparison

| Method | Reward Model | Complexity | Stability | Data Efficiency |
|--------|-------------|------------|-----------|-----------------|
| RLHF (PPO) | Yes | High | Unstable | High |
| DPO | No | Medium | Stable | Medium |
| RRHF | No | Low | Stable | Low |
| KTO | No | Low | Stable | Very high |

## Challenges

| Challenge | Description | Mitigation |
|-----------|-------------|------------|
| Reward hacking | Model exploits reward signal | KL penalty, diverse eval |
| Preference inconsistency | Human raters disagree | Aggregation, clear guidelines |
| Capability collapse | Model forgets skills during alignment | Mixed training, replay |
| Scalable oversight | Evaluating superhuman models | Debate, recursive reward modeling |

## Evaluation

- **Helpfulness**: Human eval, MT-Bench, AlpacaEval
- **Harmlessness**: Safety benchmarks, red-teaming
- **Honesty**: Calibration, refusal rates, hallucination benchmarks
- **Preference win rate**: Elo ratings, pairwise comparisons
