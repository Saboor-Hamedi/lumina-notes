---
id: a1b2c3d4-1116-4000-8000-000000000116
title: LoRA and Parameter-Efficient Fine-Tuning
language: markdown
tags:
  - ai-ml
  - deep-learning
  - peft
  - fine-tuning
selection: null
isPinned: false
timestamp: 1781795658458
---
# LoRA and Parameter-Efficient Fine-Tuning

LoRA (Low-Rank Adaptation) fine-tunes large models by training low-rank matrices injected into attention layers — achieving full fine-tuning quality with < 1% of the parameters.

## The Problem

Full fine-tuning adjusts all model parameters:
- Llama 70B: 140 GB for model + 280 GB for optimizer states + gradients
- Requires 8+ A100 GPUs
- Produces many full-size copies of the model

## How LoRA Works

```
Standard Linear Layer:        LoRA-adapted Layer:
     Input                         Input
       │                            │
  ┌────┴────┐                  ┌────┴────┐
  │ Weight W │ (d×k)           │ Weight W │ (frozen)
  └────┬────┘                  └────┬────┘
       │                            │
       │                     ┌──────┴──────┐
       │                     │  ┌──┐ ┌──┐  │
       │                     │  │ A│ │ B│  │ ← trainable
       │                     │  │r×k│ │d×r│  │
       │                     │  └──┘ └──┘  │
       │                     │     +       │
       │                     │ h = Wx + BAx│
       │                     └──────┬──────┘
       │                            │
     Output                       Output
```

- W (original) stays frozen
- A and B are low-rank (r = 4-64)
- BA approximates ΔW (the weight update)
- r × 4 → 8 → 16 → 32 → 64 (small r = small adaptation budget)

## LoRA Configuration

```python
from peft import LoraConfig, get_peft_model

lora_config = LoraConfig(
    r=16,                    # rank
    lora_alpha=32,           # scaling factor (ΔW * α/r)
    target_modules=[         # which modules to adapt
        "q_proj",            # query projection
        "v_proj",            # value projection
        "k_proj",            # key projection
        "o_proj",            # output projection
        "gate_proj",         # MLP gate (in MoE models)
    ],
    lora_dropout=0.05,       # regularization
    bias="none",
    task_type="CAUSAL_LM",
)

model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-2-7b-hf")
model = get_peft_model(model, lora_config)
model.print_trainable_parameters()
# trainable params: 8,388,608 / 6,738,415,616 → 0.12% of total
```

## QLoRA (Quantized LoRA)

Combines LoRA with 4-bit quantization of the base model:

```python
from transformers import BitsAndBytesConfig
from peft import LoraConfig, get_peft_model

bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch.bfloat16,
)

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    quantization_config=bnb_config,
    device_map="auto",
)

model = get_peft_model(model, lora_config)
```

**Result**: Fine-tune a 7B model on a single 24GB GPU.

## Memory Comparison (Llama 7B)

| Method | VRAM (Training) | VRAM (Inference) | Performance vs Full FT |
|--------|----------------|------------------|----------------------|
| Full FT (FP16) | 112 GB | 14 GB | Baseline |
| LoRA (FP16) | 16 GB | 14 GB | 99-100% |
| QLoRA (NF4) | 10 GB | 4 GB | 98-99% |
| QLoRA (NF4, double quant) | 8 GB | 3.5 GB | 98% |

## Other PEFT Methods

| Method | How | Parameters Trained |
|--------|-----|-------------------|
| LoRA | Low-rank adaptation of weight matrices | 0.1-1% |
| Adapters | Small bottleneck layers between transformer layers | 3-6% |
| Prefix Tuning | Learn virtual tokens prepended to input | 0.1% |
| P-Tuning | Learn continuous prompts in embedding space | 0.01% |
| IA3 | Element-wise scaling vectors (even smaller than LoRA) | 0.01% |

## When to Use Each

| Scenario | Recommendation |
|----------|---------------|
| Consumer GPU (8-24 GB) | QLoRA |
| Single GPU (24-48 GB) | LoRA (FP16 or INT8) |
| Multiple GPUs | LoRA full precision |
| Production inference many models | LoRA at FP16 (no quantization overhead) |
| Rapid iteration | QLoRA (fast training, slower inference) |

**Links**: [[Quantization for LLMs]] | [[Pre-training and Fine-tuning]] | [[Model Distillation]] | [[Inference Optimization]] | [[LLM Alignment]]
