---
id: a1b2c3d4-1074-4000-8000-000000000074
title: Synthetic Data Generation
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001074
---
# Synthetic Data Generation

Synthetic data is artificially generated data that mimics real-world data. It's used when real data is scarce, sensitive, or biased.

## Why Synthetic Data?

| Problem | Solution |
|---------|----------|
| Privacy concerns (PII, HIPAA, GDPR) | Generate realistic but fake data |
| Imbalanced classes | Oversample minority classes synthetically |
| Rare events | Create realistic edge cases |
| Data scarcity | Generate unlimited training data |
| Cost | Cheaper than collecting and labeling real data |

## Generation Methods

| Method | Description | Quality | Best For |
|--------|-------------|---------|----------|
| Rule-based | Hand-coded rules + random sampling | Low-Med | Structured data, forms |
| Statistical | Sample from fitted distributions | Med | Numerical tabular data |
| GANs (GAN) | Generator vs discriminator | High | Images, time series |
| VAEs (VAE) | Variational autoencoder | High | Images, embeddings |
| Diffusion models | Denoising diffusion (Sora, Stable Diffusion) | Highest | Images, video, audio |
| LLM-based | Prompt with examples, generate more | High | Text, code, conversations |

## Tabular Data (SDV)

```python
from sdv.single_table import CTGANSynthesizer
from sdv.evaluation import evaluate

# Train synthesizer on real data
synthesizer = CTGANSynthesizer()
synthesizer.fit(real_data)

# Generate synthetic rows
synthetic_data = synthesizer.sample(num_rows=10000)

# Evaluate quality
report = evaluate(synthetic_data, real_data)
print(report.get_score())  # 0-100, higher is better

# Enforce specific conditions
synthetic_data = synthesizer.sample_conditions(
    conditions={"product_category": "electronics"},
    num_rows=500
)
```

## LLM-Based Text Generation

```python
from openai import OpenAI

client = OpenAI()

prompt = """
Generate a synthetic customer support conversation.
Domain: E-commerce returns
Customer issue: Received wrong item
Tone: Frustrated customer, helpful agent

Example format:
Customer: I received a blue sweater but I ordered a red one.
Agent: I apologize for the error. Let me help you with a return.

Generate 5 more conversations with different scenarios:
"""

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": prompt}]
)
```

## Image Data (Stable Diffusion)

```python
from diffusers import StableDiffusionPipeline
import torch

pipe = StableDiffusionPipeline.from_pretrained("runwayml/stable-diffusion-v1-5")
pipe = pipe.to("cuda")

prompt = "A product photo of a red coffee mug on a wooden table, studio lighting"
image = pipe(prompt, num_inference_steps=50, guidance_scale=7.5).images[0]
image.save("synthetic_mug.png")
```

## Evaluation Metrics

| Metric | What It Measures |
|--------|-----------------|
| Statistical similarity | Distribution match (KS test, Wasserstein) |
| Field correlation | Do correlations match real data? |
| Privacy risk | Can synthetic records be traced to real people? |
| Utility | Does model trained on synthetic perform on real? |

## Utility Testing

```python
# Train on synthetic, test on real
model_synth = train_on(synthetic_data)
score_on_real = evaluate(model_synth, real_test_data)

# Train on real, test on real (baseline)
model_real = train_on(real_train_data)
baseline = evaluate(model_real, real_test_data)

# Utility = score_on_real / baseline (closer to 1 = better)
print(f"Utility: {score_on_real / baseline:.2%}")
```

## Privacy Considerations

| Technique | Protection | Pros | Cons |
|-----------|-----------|------|------|
| Differential privacy | Adds calibrated noise | Strong mathematical guarantees | Reduced utility |
| K-anonymity | Generalize fields | Simple | Loses granularity |
| Differential privacy GANs | DP training of generator | Good utility + privacy | Complex to tune |

## When NOT to Use Synthetic Data

| Scenario | Why |
|----------|-----|
| Long-tail anomalies | Synthetic methods miss rare patterns |
| Causal inference | Correlations may not reflect true causality |
| High-stakes decisions | Synthetic data can't replace real validation |
| Time series with complex dependencies | Hard to capture temporal dynamics |

**Links**: [[Data Augmentation for NLP]] | [[Computer Vision]] | [[Responsible and Ethical AI]] | [[LLM Safety and Guardrails]] | [[Privacy]]
