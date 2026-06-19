---
tags: [nlp, llm, glossary]
---

# 10 — Glossary

| Term | Definition |
|------|------------|
| **Token** | Atomic unit of text (word or subword) the model processes |
| **Context Window** | Maximum number of tokens the model can attend to |
| **KV Cache** | Cached key-value pairs from attention layers to speed generation |
| **Temperature** | Controls randomness of output (low = deterministic, high = creative) |
| **Top-p / Top-k** | Sampling strategies to filter low-probability tokens |
| **Logits** | Raw unnormalized scores from the LM head |
| **Softmax** | Converts logits into a probability distribution |
| **Autoregressive** | Generating one token at a time, feeding each back as input |
| **In-Context Learning** | Learning from examples in the prompt (no weight updates) |
| **MoE (Mixture of Experts)** | Sparse architecture where only a subset of parameters activates per token |
| **RoPE** | Rotary Position Embedding — encodes position via rotation matrices |
| **Flash Attention** | IO-aware exact attention algorithm (faster, less memory) |
| **Speculative Decoding** | Draft model predicts tokens; LLM verifies in parallel for speed |
| **SFT (Supervised Fine-Tuning)** | Training on labeled instruction-response pairs |
| **RLHF** | Reinforcement Learning from Human Feedback — alignment via preferences |
| **DPO** | Direct Preference Optimization — RLHF without separate reward model |
| **LoRA / QLoRA** | Low-Rank Adaptation — parameter-efficient fine-tuning |
| **PagedAttention** | OS-like page management for KV cache (used in vLLM) |
| **Perplexity** | How surprised the model is by text (lower = better) |
| **Beam Search** | Keeps top-b candidate sequences during generation |
| **Embedding** | Dense vector representation of a token in high-dimensional space |
| **Attention Mask** | Controls which tokens can attend to which (causal vs. bidirectional) |
| **Residual Connection** | Skip connection that lets gradients flow directly through the network |
| **Layer Norm** | Normalizes activations across feature dimension (stabilizes training) |
| **SWA (Sliding Window Attention)** | Attention limited to a local window |
| **GQA (Grouped Query Attention)** | Multiple query heads share fewer key/value heads |
| **MLA (Multi-head Latent Attention)** | Compresses KV cache via latent vectors (DeepSeek) |

**Links**: [[AI-ML/NLP/LLM/01 Architecture Overview]] | [[AI-ML/NLP/LLM/02 Tokenization & Generation]] | [[AI-ML/NLP/LLM/09 Models, Trends & Selection]]
**See also**: [[Tokenization]] | [[Transformer Architecture]] | [[Attention Mechanism]]
