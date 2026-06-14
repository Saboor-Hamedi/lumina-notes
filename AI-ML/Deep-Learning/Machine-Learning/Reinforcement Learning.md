---
id: a1b2c3d4-1032-4000-8000-000000000032
title: Reinforcement Learning
language: markdown
tags: [ai-ml, machine-learning, reinforcement-learning, rl]
selection: null
isPinned: false
timestamp: 1781500001032
---
# Reinforcement Learning

Reinforcement Learning (RL) trains agents to make sequential decisions by rewarding desired behavior and penalizing undesired behavior.

## Core Components

| Component | Description |
|-----------|-------------|
| Agent | Decision maker / learner |
| Environment | The world the agent interacts with |
| State (s) | Current situation |
| Action (a) | What the agent can do |
| Reward (r) | Feedback signal (scalar) |
| Policy (π) | Maps state → action (what to do) |
| Value (V) | Expected cumulative future reward |

## The RL Loop

```
Agent
  │
  │ action (a)
  ▼
Environment ──→ next state (s') + reward (r)
  │
  └────────────────────────→ Agent updates policy
```

## Key Algorithms

| Algorithm | Type | Description |
|-----------|------|-------------|
| Q-Learning | Value-based | Learns optimal action-value function |
| Deep Q-Network | Value-based | Q-learning with neural networks |
| Policy Gradients | Policy-based | Directly optimize policy |
| PPO | Policy-based | Stable, clipped policy updates |
| DDPG | Actor-Critic | Continuous actions |
| SAC | Actor-Critic | Maximum entropy RL |
| A3C | Actor-Critic | Asynchronous training |

## Exploration vs Exploitation

| Strategy | Behavior |
|----------|----------|
| ε-greedy | Mostly best action, random ε% of time |
| Softmax | Sample actions proportional to value |
| UCB | Pick actions with highest uncertainty |
| Thompson Sampling | Sample from belief distribution |

## Markov Decision Process (MDP)

Formalized as (S, A, P, R, γ):
- **S**: Set of states
- **A**: Set of actions
- **P**: Transition probability P(s' | s, a)
- **R**: Reward function R(s, a)
- **γ**: Discount factor (0-1, near 0 = myopic, near 1 = far-sighted)

## Bellman Equation

```
V(s) = max_a [ R(s,a) + γ * Σ P(s'|s,a) * V(s') ]
```

The value of a state = immediate reward + discounted future value.

## Applications

| Domain | Example |
|--------|---------|
| Games | AlphaGo, Dota 2, chess |
| Robotics | Locomotion, grasping, manipulation |
| Recommendation | Content personalization |
| Autonomous driving | Lane changing, navigation |
| Resource management | Data center cooling, traffic signals |
| LLM alignment | RLHF (Reinforcement Learning from Human Feedback) |

## RLHF (Reinforcement Learning from Human Feedback)

```
LLM generates responses → Human ranks them → Reward model trained on rankings
    ↓
PPO optimizes LLM policy using reward model signal
```

Used by ChatGPT, Claude, Gemini to align models with human preferences.

**Links**: [[Pre-training and Fine-tuning]] | [[Transformer Architecture]] | [[NLP Pipeline Design]] | [[Decision Trees and Random Forests]] | [[Gradient Boosting]]
