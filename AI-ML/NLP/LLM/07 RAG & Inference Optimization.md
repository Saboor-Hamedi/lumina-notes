---
tags: [nlp, llm, rag, inference, quantization]
---

# 07 — RAG & Inference Optimization

## RAG Integration Architecture

```mermaid
graph TB
    subgraph RAG["Retrieval-Augmented Generation"]
        Q[Query] --> E[Embedding Model]
        E --> VS[Vector Database]
        VS --> DOCS[Top-K Chunks]
        DOCS --> CTX[Context + Query]
    end
    subgraph Direct["Direct Generation"]
        Q2[Query] --> CTX2[Query Only]
    end
    subgraph Generation["LLM Generation"]
        CTX --> LLM[LLM]
        CTX2 --> LLM
        LLM --> RESP[Grounded Response + Citations]
    end
    RAG --> Generation
    Direct --> Generation
```

| Approach | Best For |
|----------|----------|
| **No RAG** | Casual chat |
| **RAG** | Document QA, support |
| **RAG + Re-rank** | Enterprise search |
| **Agentic RAG** | Research assistants |

## Inference Optimization

| Technique | Speed Gain | Memory Impact |
|-----------|-----------|---------------|
| **KV Cache** | 2-4× faster | Grows with context |
| **Speculative Decoding** | 2-3× faster | +Draft model |
| **Flash Attention** | 2-4× faster | Reduced |
| **Quantization** | 2-4× smaller model | Slightly lower quality |
| **Batching** | Higher throughput | Higher latency |
| **PagedAttention (vLLM)** | 2-4× throughput | Better utilization |

## Quantization Levels

```mermaid
graph LR
    FP32["FP32"] --> FP16["FP16"]
    FP16 --> BF16["BF16"]
    BF16 --> INT8["INT8"]
    INT8 --> INT4["INT4"]
    INT4 --> NF4["NF4"]
```

### Memory by Model Size

| Size | FP16 | INT8 | INT4 | Consumer GPU? |
|------|------|------|------|---------------|
| 7B | 14 GB | 7 GB | 3.5 GB | ✅ RTX 3090+ |
| 13B | 26 GB | 13 GB | 6.5 GB | ✅ RTX 4090 |
| 34B | 68 GB | 34 GB | 17 GB | ✅ RTX 4090 (INT4) |
| 70B | 140 GB | 70 GB | 35 GB | ⚠️ Dual GPU |
| 405B | 810 GB | 405 GB | 200 GB | ❌ Cloud only |

**Links**: [[AI-ML/NLP/LLM/04 Fine-Tuning]] | [[AI-ML/NLP/LLM/02 Tokenization & Generation]] | [[AI-ML/NLP/LLM/09 Models, Trends & Selection]]
**See also**: [[RAG]], [[Vector Databases]], [[LLM Evaluation and Benchmarks]]
