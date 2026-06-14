---
tags: [nlp, llm, models, trends, selection]
---

# 09 — Models, Trends & Selection

## Notable Models Timeline

| Year | Model | Significance |
|------|-------|-------------|
| 2018 | GPT-1, BERT | First transformer LMs |
| 2019 | GPT-2, T5 | Scaling up |
| 2020 | GPT-3 | In-context learning at scale |
| 2022 | ChatGPT, PaLM | Chat-optimized, chain-of-thought |
| 2023 | GPT-4, LLaMA, Claude 3 | Frontier capability, open-weight |
| 2024 | LLaMA 3, DeepSeek-V3, Gemma | Open-weight parity, MoE efficiency |

## Model Comparison

| Model | Params | Context | Open? | Standout Feature |
|-------|--------|---------|-------|------------------|
| **GPT-4** | ~1.8T (est.) | 128K | No | Broad capability |
| **Claude 3.5** | Unknown | 200K | No | Long context, safety |
| **LLaMA 3** | 8B — 405B | 128K | Yes | Strong open-weight |
| **Mistral** | 7B — 8×22B | 32K — 128K | Yes | Efficient MoE |
| **Gemini 1.5** | Unknown | 1M+ | No | Massive context |
| **DeepSeek-V3** | 671B MoE | 128K | Yes | Cost-efficient |
| **Qwen 2.5** | 0.5B — 72B | 128K | Yes | Multilingual |

## Key Capabilities (by Maturity)

- **Mature & High Quality**: Translation, Summarization, Code Generation
- **Emerging & High Quality**: Creative Writing, Tool Use
- **Emerging & Lower Quality**: Long-Form Reasoning, Multimodal, Planning, Causal Reasoning

## Limitations

| Limitation | Mitigation |
|------------|------------|
| Hallucination | RAG, grounding, factual checks |
| Context window limits | RAG, attention optimization |
| Knowledge cutoff | Web search, RAG |
| Cost | Quantization, caching, distillation |
| Latency | Speculative decoding, KV cache |

## Model Selection Decision Tree

- **Budget:** Free/Low → Consumer GPU (24GB): LLaMA 3 8B / Mistral 7B / Qwen 7B
- **Budget:** Free/Low → CPU/Mobile: Phi-3, Gemma 2B, Llama 3.2 1-3B
- **Budget:** Medium → API: Claude 3.5 Sonnet / GPT-4o mini
- **Budget:** Medium → Self-host + customize: LLaMA 3 70B / Qwen 72B
- **Budget:** High → API: GPT-4o / Claude 3.5 / Gemini 1.5 Pro
- **Budget:** High → Self-host + privacy: LLaMA 3 405B / DeepSeek-V3

## 2025-2026 Trends

| Trend | Impact | Maturity |
|-------|--------|----------|
| Reasoning Models (o1-style) | Better math/code | Early |
| Extreme Long Context | "Infinite" windows | Early-Mid |
| Small Language Models | On-device AI | Mature |
| Agentic Workflows | Autonomous tasks | Early |
| Native Multimodal | Unified understanding | Mid |
| Test-Time Compute | Spend compute at inference | Early |

## The Cost Equation

| Factor | Cost Driver | Optimize By |
|--------|-------------|-------------|
| Pre-training | GPU-hours | MoE, data quality |
| Fine-tuning | Steps × model size | LoRA, QLoRA |
| Input tokens | Context length | Caching |
| Output tokens | Generation length | Early stopping |
| Serving infra | GPU count | Batching, quantization |

## Scaling Laws

- Loss decreases predictably with more parameters and more data
- Compute-optimal ratio: ~20 tokens per parameter (Chinchilla)
- Most models were undertrained before Chinchilla
- Quality gap: 7B→70B: +15-25%, 70B→405B: +5-10%, 405B→1T+: +2-5%

**Links**: [[AI-ML/NLP/LLM/03 Training & Data]] | [[AI-ML/NLP/LLM/08 Safety, Evaluation & Hallucination]] | [[AI-ML/NLP/LLM/10 Glossary]]
**See also**: [[Scaling Laws]] | [[LLM Evaluation and Benchmarks]] | [[GPT and Decoder Models]]
