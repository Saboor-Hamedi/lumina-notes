---
id: a1b2c3d4-1050-4000-8000-000000000050
title: Speculative Decoding
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001050
---
# Speculative Decoding

Speculative decoding speeds up LLM inference by using a small, fast draft model to propose tokens, which are then verified by the large target model in parallel.

## The Problem

Standard autoregressive decoding generates one token at a time, each requiring a full forward pass through the target model. This is memory-bound and slow.

## How it Works

```
Target Model: Large, slow, high quality  (e.g., Llama 70B)
Draft Model:  Small, fast, decent quality (e.g., Llama 68M)

Step 1: Draft model generates K tokens quickly (autoregressive)
Step 2: Target model verifies all K tokens in ONE forward pass
Step 3: Reject tokens where target disagrees, keep accepted prefix
Step 4: Resample from target distribution at first rejection point
```

## Rejection Sampling

```python
def verify(draft_tokens, target_logits, draft_logits):
    accepted = []
    for i, token in enumerate(draft_tokens):
        p_target = softmax(target_logits[i])
        p_draft = softmax(draft_logits[i])
        
        if p_target[token] > p_draft[token]:
            # Always accept if target assigns higher probability
            accepted.append(token)
        else:
            # Reject with probability proportional to ratio
            if random() < p_target[token] / p_draft[token]:
                accepted.append(token)
            else:
                # Resample from (p_target - p_draft)+ distribution
                resampled = sample_from_corrected(p_target, p_draft)
                accepted.append(resampled)
                break
    return accepted
```

The verification is **lossless** — the output distribution is identical to running the target model autoregressively.

## Speedup Factors

| Draft Model Size | Target Model Size | Typical Speedup |
|-----------------|-------------------|-----------------|
| 68M | 7B | 2-3x |
| 68M | 70B | 3-4x |
| 1.5B | 70B | 2-2.5x |

Speedup depends on:
- Acceptance rate (how often draft is correct)
- Draft/target cost ratio
- Batch size for verification

## Draft Model Options

| Approach | Draft Source | Pros | Cons |
|----------|-------------|------|------|
| Small LM | Trained smaller model | Good acceptance | Extra training |
| Same model, earlier layer | Use early exit | No extra params | Lower acceptance |
| N-gram/retrieval | Lookup table | Zero extra params | Limited to training data |
| Multi-token prediction | Predict n tokens at once | Learned shortcuts | Training changes needed |

## Advantages

| Benefit | Why |
|---------|-----|
| Lossless | Output distribution matches target model |
| No training needed | Draft model can be off-the-shelf |
| Works with any target | Doesn't change target model architecture |
| Composable | Can combine with quantization, caching |

## Limitations

- Requires draft model on same device (memory overhead)
- Acceptance rate varies by task
- Draft model generation is still autoregressive (can be slow locally)
- Maximum speedup bounded by draft/target cost ratio

## Implementation

```python
# With Hugging Face Transformers
from transformers import AutoModelForCausalLM

target = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-2-70b-hf")
draft = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-2-68m-hf")

# Use assisted decoding
outputs = target.generate(
    input_ids=input_ids,
    assistant_model=draft,  # draft model for speculation
    num_assistant_tokens=5  # number of draft tokens per step
)
```

**Links**: [[Inference Optimization]] | [[KV Cache and Inference]] | [[Flash Attention]] | [[Transformer Architecture]] | [[Quantization for LLMs]]
