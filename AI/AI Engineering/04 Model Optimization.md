# 04 — Model Optimization

**Links**: [[_MOC]] | [[02 Model Serving]] | [[03 Distributed Training]] | [[05 Production LLM]]

Optimization reduces model size, memory, and latency while preserving accuracy. Essential for deploying large models under production constraints.

## Quantization

Reduces the precision of model weights and activations from FP16 to lower bit-widths.

| Method | Bits | Memory Savings | Accuracy | When To Use |
|--------|------|---------------|----------|-------------|
| **FP16 default** | 16 | 1× (baseline) | — | Dev, baseline |
| **INT8 (W8A8)** | 8 | 2× | Negligible | Small models, latency-sensitive |
| **INT4 (W4A16)** | 4 | 4× | Slight drop | Large models, memory-bound |
| **INT4 + KV cache 8** | 4+8 | 6×+ | Slight drop | LLM serving at scale |

### Quantization Techniques

| Technique | Type | Calibration | Example Tools |
|-----------|------|------------|--------------|
| **Post-Training Quantization (PTQ)** | Static | Small dataset (100-1000 samples) | `torch.ao.quantization`, ONNX Runtime |
| **GPTQ** | PTQ, layer-wise | 128 samples | `auto_gptq`, `exllama` |
| **AWQ** | PTQ, activation-aware | 128 samples | `autoawq`, `vLLM` |
| **BitsAndBytes (BNB)** | PTQ, NF4 | None (data-free) | `bitsandbytes`, QLoRA |
| **Quantization-Aware Training (QAT)** | Training | Training data | `torch.ao.quantization`, `TensorRT` |

```python
# AWQ: activation-aware quantization
from awq import AutoAWQForCausalLM

model = AutoAWQForCausalLM.from_pretrained("meta-llama/Llama-2-7b-chat-hf")
model.quantize(tokenizer, quant_config={"w_bit": 4, "version": "GEMM"})
model.save_quantized("llama-7b-awq")
```

### KV Cache Quantization

LLMs store past key-value pairs for each token — this grows linearly with sequence length and batch size:

- **Default**: FP16 KV cache = 2 bytes × 2 (K+V) × layers × hidden_dim × seq_len × batch
- **KV cache quantization**: FP8 or INT4 → 2-4× memory savings
- **Techniques**: SmoothQuant for KV cache, KIVI, KVQuant

## Pruning

Removes redundant weights while preserving model behavior:

| Type | What | Sparsity | Accuracy Impact |
|------|------|----------|----------------|
| **Unstructured** | Zero out individual weights | 50-90% | Gradual drop, NVIDIA Sparse Tensor Cores 2× speedup at 2:4 |
| **Structured** | Remove entire neurons/channels/heads | 20-50% | Higher drop, but actual speedup on any hardware |
| **Attention head pruning** | Remove redundant attention heads | Up to 30% | Minimal for large models |

## Knowledge Distillation

Train a smaller "student" model to mimic a larger "teacher":

- **Black-box distillation**: Train student on teacher's output probabilities (soft labels)
- **White-box distillation**: Match intermediate layer representations
- **Application**: Distill GPT-4 → LLaMA, BERT-large → BERT-base

## Speculative Decoding

Use a small "draft" model to propose multiple tokens, then verify them in parallel with the large model:

```
Draft model (e.g., 1B): proposes [the, cat, sat, on, the]
Large model (e.g., 70B): verifies all 5 in one forward pass
                        → accepts [the, cat, sat, on]
                        → rejects [the], corrects with [mat]
                        → net: 4 tokens for 1 pass of large model
```

2-3× throughput improvement with no quality loss (output distribution matches the large model exactly).

## ONNX & TensorRT

| Tool | Purpose | Benefit |
|------|---------|---------|
| **ONNX** | Interchange format, graph optimization | Maximum framework compatibility |
| **ONNX Runtime** | Cross-platform inference engine | Graph optimization, quantization, DML/CUDA/CPU |
| **TensorRT** | NVIDIA GPU optimization | Kernel fusion, FP8/INT8 tensor cores, layer fusion |
| **TensorRT-LLM** | LLM-specific TensorRT | In-flight batching, paged KV cache, quantization plugins |

**Links**: [[02 Model Serving]] | [[03 Distributed Training]] | [[05 Production LLM]] | [[AI-ML/NLP/Inference Optimization]]
