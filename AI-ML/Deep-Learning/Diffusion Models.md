---
id: new-ai-024-0000-0000-0000-000000000024
title: Diffusion Models
language: markdown
tags: [deep-learning, computer-vision, generative-ai]
isPinned: false
timestamp: 1781900000024
---

# Diffusion Models

**Links**: [[Computer Vision]] | [[Pre-training and Fine-tuning]] | [[Transformer Architecture]] | [[Multi-Modal RAG]] | [[LoRA and Parameter-Efficient Fine-Tuning]]

**See also**: [[Scaling Laws]], [[Inference Optimization]], [[Responsible and Ethical AI]], [[LLM Safety and Guardrails]]

## Overview

Diffusion models generate data by learning to reverse a gradual noising process. They power DALL-E, Stable Diffusion, Midjourney, and Sora.

## The Two Processes

### Forward Process (Noising)

Gradually add Gaussian noise to data over T steps:

```math
q(x_t | x_{t-1}) = N(x_t; sqrt(1-β_t) * x_{t-1}, β_t * I)
```

After enough steps: x_T ~ N(0, I)

### Reverse Process (Denoising)

Learn to predict and remove noise:

```math
p_θ(x_{t-1} | x_t) = N(x_{t-1}; μ_θ(x_t, t), Σ_θ(x_t, t))
```

## Training Objective

```python
# Simplified training loop
for x_0 in dataloader:          # Clean image
    t = randint(1, T)           # Random timestep
    noise = randn_like(x_0)     # Sample noise
    x_t = sqrt(α_bar_t) * x_0 + sqrt(1 - α_bar_t) * noise

    # Predict added noise
    noise_pred = model(x_t, t)
    loss = mse(noise_pred, noise)
```

## Sampling (DDPM)

```python
def sample(model, T, img_size):
    x_T = randn(img_size)       # Pure noise

    for t in reversed(range(T)):
        noise_pred = model(x_t, t)
        noise = randn_like(x_t) if t > 1 else 0

        # Remove predicted noise
        x_{t-1} = 1/sqrt(α_t) * (x_t - (1-α_t)/sqrt(1-α_bar_t) * noise_pred) + σ_t * noise

    return x_0
```

## Architecture: U-Net + Attention

```
Input (latent)
    ↓
Encoder Block 1 ──────────→ Skip Connection ──┐
    ↓                                           │
Encoder Block 2 ──────────→ Skip Connection ──┤
    ↓                                           │
Bottleneck (Self-Attention + Cross-Attention)   │
    ↓                                           │
Decoder Block 2 ←───────── Skip Connection ────┤
    ↓                                           │
Decoder Block 1 ←───────── Skip Connection ────┘
    ↓
Output (denoised latent)
```

## Latent Diffusion (Stable Diffusion)

Operate in compressed latent space (not pixel space):

```
Encoder: Image → Latent (48x compress)
Diffusion: Denoise in latent space
Decoder: Latent → Image

Memory: 1000x reduction over pixel-space diffusion
```

## Conditioning (Text-to-Image)

Cross-attention between U-Net and text embeddings:

```python
# Text conditioning via cross-attention
text_embeddings = text_encoder(prompt)  # CLIP or T5
image_features = unet(latent, t, context=text_embeddings)
```

## Classifier-Free Guidance

```python
# Interpolate between conditional and unconditional
ϵ_θ = ϵ_uncond + guidance_scale * (ϵ_cond - ϵ_uncond)

# guidance_scale = 7.5 (typical)
# Higher = more aligned with prompt, less diverse
```

## Sampling Speed

| Method | Steps | Quality | Speedup |
|--------|-------|---------|---------|
| DDPM | 1000 | Best | 1x |
| DDIM | 50 | Very good | 20x |
| DPM Solver | 10-20 | Good | 50-100x |
| LCM (Latent Consistency) | 1-4 | Decent | 250-1000x |

## Key Models

| Model | Type | Parameters | Features |
|-------|------|------------|----------|
| Stable Diffusion 3 | Latent Diffusion | 800M-8B | MMDiT, text rendering |
| DALL-E 3 | Transformer + Diffusion | ~12B | Caption improvement, safety |
| Sora | Diffusion + Transformer | ~3B | Video generation |
| Imagen | Pixel Diffusion | ~3B | Cascaded, text-only embedding |
| Flux | Latent Diffusion | 12B | SOTA quality, fast |

## Cross-Domain Connections

- [[Computer Vision]] — primary application domain
- [[Multi-Modal RAG]] — generating images in retrieval-augmented systems
- [[LoRA and Parameter-Efficient Fine-Tuning]] — fine-tuning diffusion models
- [[Transformer Architecture]] — backbone for modern diffusion models (DiT, MMDiT)
- [[Scaling Laws]] — how diffusion models scale with compute
- [[Inference Optimization]] — making generation faster
