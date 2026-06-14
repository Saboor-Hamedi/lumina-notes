---
id: a1b2c3d4-1055-4000-8000-000000000055
title: LLM Alignment
language: markdown
tags: [ai-ml, nlp]
selection: null
isPinned: false
timestamp: 1781500001055
---
# LLM Alignment

LLM alignment steers models to behave according to human values, preferences, and intent — making them helpful, harmless, and honest.

## Why Alignment?

| Problem | Example |
|---------|---------|
| Helpfulness | Model refuses to answer or gives useless responses |
| Harmlessness | Model provides dangerous instructions |
| Honesty | Model confabulates confidently wrong answers |
| Bias | Model exhibits demographic/social biases |
| Sycophancy | Model agrees with user even when wrong |

## Alignment Taxonomy

```
Pre-training → Supervised Fine-tuning → RLHF → Constitutional AI
   ↓                    ↓                  ↓            ↓
Broad corpus        Task examples      Preference     Self-review
(baseline)          (format, style)    (what's best)  (principles)
```

## RLHF (Reinforcement Learning from Human Feedback)

### Step 1: SFT (Supervised Fine-Tuning)

Train on high-quality demonstrations of desired behavior.

### Step 2: Reward Model Training

```
Prompt → Model → Response A ⟶ Human: A is better than B
Prompt → Model → Response B ⟶ Human: A is better than B

Loss = -log(σ(reward(A) - reward(B)))
```

Train a reward model to predict human preferences.

### Step 3: PPO Optimization

```
Reward Model → score → maximize via PPO
                      ↓
            Policy model (frozen)
                      ↓
            KL penalty (don't drift too far)
```

```python
# PPO objective with KL penalty
loss = -E[reward - β * KL(π_new || π_ref)]
```

**β**: Controls how much the model can change (typical: 0.01-0.1)

## DPO (Direct Preference Optimization)

Simpler alternative to RLHF — no reward model needed:

```python
loss = -log σ(β * (log(π(y_w|x) / π_ref(y_w|x)) - log(π(y_l|x) / π_ref(y_l|x))))
```

| Aspect | RLHF | DPO |
|--------|------|-----|
| Reward model | Required | No |
| Training stability | Complex (PPO) | Simple (binary cross-entropy) |
| Compute | 2 models (policy + reward) | 1 model + reference |
| Performance | Similar | Similar |

## Constitutional AI (Claude)

Self-supervised alignment without human feedback:

```
Step 1: Model generates harmful response → Model critiques it using constitution
Step 2: Model revises response based on critique
Step 3: Train model to prefer revised response → "RL from AI Feedback" (RLAIF)
```

**Constitution example**: "Do not provide information that could directly facilitate harm to humans."

## KLR (Kahneman-Like Reinforcement)

Aligns models to be more deliberate:

| Mode | Characteristics |
|------|----------------|
| Fast (System 1) | Immediate, intuitive, prone to errors |
| Slow (System 2) | Deliberate, step-by-step, more accurate |

Training rewards models for showing reasoning before answers.

## Red Teaming

Testing alignment by adversarial probing:

| Category | Example |
|----------|---------|
| Jailbreaks | "Ignore previous instructions and..." |
| Role-playing | "You are DAN (Do Anything Now)..." |
| Encoding | Base64 encoded harmful requests |
| Multi-turn | Gradually steer conversation |

## Evaluation

| Benchmark | What It Measures |
|-----------|-----------------|
| Helpful/Harmless (HH-RLHF) | General alignment |
| TruthfulQA | Factual accuracy |
| BBQ | Bias measurement |
| AdvBench | Resistance to harmful requests |
| MMLU | Knowledge (not alignment, but checks retained capability) |

## Alignment Tax

The quality loss from alignment. Mitigations:
- **RLHF with KL penalty**: Limits drift from base model
- **DPO**: More stable, less regressive
- **Data quality**: Better preference data = less regression
- **Iterated alignment**: Multiple rounds of improvement

**Links**: [[Reinforcement Learning]] | [[Pre-training and Fine-tuning]] | [[GPT and Decoder Models]] | [[Prompt Engineering]] | [[LLM Safety and Guardrails]] | [[RLHF and Preference Optimization]] | [[Responsible and Ethical AI]] | [[LoRA and Parameter-Efficient Fine-Tuning]]
