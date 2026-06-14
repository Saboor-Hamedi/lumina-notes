---
id: dev-057-0000-0000-0000-000000000041
title: Inference Optimization
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781800000019
---
# Inference Optimization

**Links**: [[Model Quantization]] | [[GPT and Decoder Models]] | [[Transformer Architecture]] | [[Pre-training and Fine-tuning]] | [[Kubernetes Deployments]]

## Key Techniques

| Technique | Speedup | Complexity | Quality Impact |
|-----------|---------|------------|----------------|
| Quantization (INT8) | 2x | Low | Minimal |
| Quantization (INT4) | 3x | Low | Slight |
| KV Cache | Variable | Medium | None |
| Flash Attention | 2-4x | Medium | None |
| Speculative Decoding | 2-3x | High | None |
| Batching | Nx | Medium | None |

## KV Cache

Transformers cache Key and Value tensors from previous tokens to avoid recomputation.

```
Without cache: O(N²) per token
With cache:    O(N) per token (N = sequence length)
```

## Flash Attention

A memory-efficient attention algorithm that:
- Avoids materializing the full N×N attention matrix
- Uses tiling to fit in fast SRAM
- 2-4x faster on GPU

```python
# pip install flash-attn
model = AutoModelForCausalLM.from_pretrained(
    "model-name",
    attn_implementation="flash_attention_2",
    torch_dtype=torch.float16
)
```

## Speculative Decoding

Use a small draft model to propose tokens, then verify with the large model.

```
Draft model predicts: ["The", "cat", "sat", "on"]
Large model verifies: ✅ ✅ ✅ ✅  (accepts all 4 at once!)
```

## Batching

Process multiple requests simultaneously on GPU:

```python
# Dynamically batch requests
from vllm import LLM, SamplingParams

llm = LLM(model="meta-llama/Llama-2-7b")
outputs = llm.generate(prompts, SamplingParams(temperature=0.1))
```

**Next**: [[Model Quantization]] — Reduce model size