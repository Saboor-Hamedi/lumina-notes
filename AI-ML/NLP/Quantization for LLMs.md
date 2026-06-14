---
id: a1b2c3d4-1054-4000-8000-000000000054
title: Quantization for LLMs
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001054
---
# Quantization for LLMs

Quantization reduces model precision (e.g., FP16 → INT4) to lower memory and compute costs with minimal quality loss.

## Precision Levels

| Precision | Bits | Memory vs FP16 | Quality |
|-----------|------|----------------|---------|
| FP32 | 32 | 2x larger | Full precision |
| FP16/BF16 | 16 | Baseline | Training default |
| INT8 | 8 | 50% smaller | Near-lossless |
| INT4/FP4 | 4 | 75% smaller | Minor degradation |
| INT2 | 2 | 87.5% smaller | Significant degradation |
| NF4 | 4 | 75% smaller | Better than INT4 (normal float) |

## Weight-Only vs Weight+Activation

| Type | What is Quantized | Use Case |
|------|------------------|----------|
| Weight-only (W4A16) | Weights only | Memory-bound inference (decode) |
| Weight + activation (W8A8) | Weights + activations | Compute-bound inference (prefill) |
| KV cache quantization | K,V cache | Long context generation |

## Post-Training Quantization (PTQ)

Quantize a trained model without retraining:

```python
# Simple RTN (Round-to-Nearest)
def quantize_weights(w, bits=4):
    scale = w.abs().max() / (2**(bits-1) - 1)
    q = torch.round(w / scale).clamp(-2**(bits-1), 2**(bits-1)-1)
    return q, scale
```

| Method | Technique | Quality |
|--------|-----------|---------|
| RTN | Round weights to nearest | Baseline |
| GPTQ | Optimal quantization with Hessian | Excellent (W4A16) |
| AWQ | Scale-based, activation-aware | Excellent (W4A16) |
| GGUF (GGML) | CPU-optimized, many formats | Good |

## GPTQ (Optimal Brain Quantization)

Uses the Hessian (second-order info) to find optimal quantized weights:

```
For each column of weights:
  1. Quantize one weight
  2. Update remaining weights to compensate for error
  3. Repeat using Hessian-based correction
```

- 4-bit GPTQ achieves < 1% perplexity degradation on most models
- Requires a calibration dataset (128-2048 samples)

## AWQ (Activation-Aware Weight Quantization)

Observes that not all weights are equally important — weights corresponding to large activations matter more:

```python
# Scale up important channels before quantization
scales = get_activation_scales(weights, calibration_data)
scaled_weights = weights * scales  # protect important channels
q_weights = quantize(scaled_weights)
q_weights /= scales  # rescale back
```

AWQ achieves better quality than GPTQ at INT4 with similar speed.

## Bitsandbytes (QLoRA)

```python
from transformers import BitsAndBytesConfig

bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",  # Normal Float 4
    bnb_4bit_compute_dtype=torch.bfloat16,
    bnb_4bit_use_double_quant=True  # Double quantization of scales
)

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    quantization_config=bnb_config
)
```

| Feature | Benefit |
|---------|---------|
| NF4 | Better distribution fit than INT4 |
| Double quantization | Save 0.5 bits per parameter on scales |
| QLoRA | Fine-tune quantized models with LoRA |

## GGUF / GGML

Primarily for CPU inference (can offload layers to GPU):

| Quant Type | Bits | Quality Loss |
|------------|------|-------------|
| Q4_K_M | 4.5 | Very low |
| Q5_K_M | 5.5 | Minimal |
| Q8_0 | 8 | Near-lossless |
| Q2_K | 2.5 | Significant |

Format: https://github.com/ggerganov/llama.cpp

## Memory Savings

| Model | FP16 | INT8 | INT4 |
|-------|------|------|------|
| Llama 7B | 14 GB | 7 GB | 3.5 GB |
| Llama 13B | 26 GB | 13 GB | 6.5 GB |
| Llama 70B | 140 GB | 70 GB | 35 GB |
| Mixtral 8x7B | 94 GB | 47 GB | 24 GB |

INT4 enables running 70B models on consumer hardware (2× RTX 4090 24GB).

## Quality Benchmarks

| Quantization | Llama 2 7B (PPL) | Llama 2 13B (PPL) |
|-------------|------------------|-------------------|
| FP16 | 5.47 | 4.88 |
| GPTQ 4-bit | 5.55 (+0.08) | 4.95 (+0.07) |
| AWQ 4-bit | 5.52 (+0.05) | 4.93 (+0.05) |
| Bitsandbytes 4-bit | 5.62 (+0.15) | 5.01 (+0.13) |

**Links**: [[Inference Optimization]] | [[Model Quantization]] | [[Mixture of Experts]] | [[Flash Attention]] | [[KV Cache and Inference]]
