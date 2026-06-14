---
id: a1b2c3d4-1117-4000-8000-000000000117
title: Model Distillation
language: markdown
tags: [ai-ml, deep-learning, distillation, optimization]
selection: null
isPinned: false
timestamp: 1781500001117
---
# Model Distillation

Model distillation compresses a large, accurate teacher model into a smaller, faster student model by training the student to mimic the teacher's behavior.

## Why Distill?

| Reason | Impact |
|--------|--------|
| Latency | Smaller model = faster inference |
| Cost | Lower compute for training + serving |
| Deployment | Runs on edge devices, consumer hardware |
| Privacy | Teacher can stay private (student learns from API) |

## Knowledge Distillation

```
Teacher (large): Soft labels (probability distribution)
                     ↓
Student (small): Minimize loss against teacher's soft labels
                     ↓
Loss = α * KL(teacher_logits || student_logits) + (1-α) * CE(student_logits, hard_labels)
```

```python
def distill_loss(student_logits, teacher_logits, labels, temperature=3.0, alpha=0.7):
    soft_targets = F.softmax(teacher_logits / temperature, dim=-1)
    soft_preds = F.log_softmax(student_logits / temperature, dim=-1)

    distill_loss = F.kl_div(soft_preds, soft_targets, reduction="batchmean")
    distill_loss *= temperature ** 2  # scale back

    hard_loss = F.cross_entropy(student_logits, labels)

    return alpha * distill_loss + (1 - alpha) * hard_loss
```

## Temperature

| Temperature | Softmax Output |
|-------------|---------------|
| T → 0 | One-hot (hard labels) |
| T = 1 | Original probabilities |
| T → ∞ | Uniform distribution |

Higher temperature produces softer distributions — more information about class relationships (e.g., "dog" is more like "wolf" than "car").

## Distillation Variants

| Variant | Description |
|---------|-------------|
| Logit distillation | Match teacher's output logits |
| Feature distillation | Match intermediate layer representations |
| Self-distillation | Student learns from itself (train → deeper train) |
| Online distillation | Student + teacher train simultaneously |
| Data-free distillation | Generate synthetic data from teacher |
| Quantization-aware distillation | Distill during quantization |

## White-Box vs Black-Box

| Type | Access | Example |
|------|--------|---------|
| White-box | Full model (weights, logits) | Open-source teacher |
| Black-box | API only (text output) | GPT-4 → small model |
| Grey-box | Logits via API | Some hosted models |

## Data Distillation (Dataset Distillation)

Compress the training dataset:

```
Original: 50K images → train student directly → 80% accuracy
Distilled: 10 synthetic images per class → train student → 78% accuracy
```

Used for: efficient hyperparameter search, privacy, continual learning.

## In-Context Distillation

```python
# Use teacher to generate reasoning traces
student_prompt = """
Problem: {problem}
Teacher's reasoning: {teacher_reasoning}
Answer: {teacher_answer}
Now solve the next problem:
{next_problem}
"""
```

Train student on teacher-generated CoT traces.

## Student-Teacher Architecture Choices

| Teacher | Student | Compression |
|---------|---------|-------------|
| Llama 70B | Llama 7B | 10x |
| GPT-4 | GPT-3.5 | ~10x |
| Ensemble models | Single model | N x |
| BERT-Large | TinyBERT | 7x |

## Distillation Pipeline

```python
from transformers import AutoModelForSeq2SeqLM, AutoTokenizer

teacher = AutoModelForSeq2SeqLM.from_pretrained("google/flan-t5-xxl")
student = AutoModelForSeq2SeqLM.from_pretrained("google/flan-t5-small")

trainer = DistillationTrainer(
    teacher=teacher,
    student=student,
    train_dataset=dataset,
    temperature=4.0,
    alpha=0.5,
)

trainer.train()
```

## When Distillation Fails

| Scenario | Issue |
|----------|-------|
| Teacher too large | Student can't capture complexity |
| Capacity gap | Student too small to benefit |
| Noisy teacher | Distilling errors |
| Domain mismatch | Teacher data ≠ deployment data |
| Logit imbalance | One class dominates (use temperature tuning) |

**Links**: [[LoRA and Parameter-Efficient Fine-Tuning]] | [[Quantization for LLMs]] | [[Inference Optimization]] | [[Pre-training and Fine-tuning]] | [[Model Quantization]]
