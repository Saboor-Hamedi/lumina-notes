---
tags: [nlp, llm, safety, evaluation, hallucination]
---

# 08 — Safety, Evaluation & Hallucination

## LLM Safety & Guardrails Stack

```mermaid
graph TB
    subgraph InputGuard["Input Guardrails"]
        G1[Prompt Injection Detection] --> G2[Toxicity / PII Filter]
        G2 --> G3[Token Limit Check]
    end
    subgraph ModelGuard["Model-Level Safety"]
        M1[RLHF / DPO Training]
        M2[Refusal Training]
        M3[Constitutional AI]
    end
    subgraph OutputGuard["Output Guardrails"]
        O1[Content Moderation]
        O2[Factuality Check]
        O3[Format Validation]
    end
    InputGuard --> ModelGuard --> OutputGuard
```

## Evaluation Ecosystem

| Method | Examples |
|--------|----------|
| **Automated Benchmarks** | MMLU, GSM8K, HumanEval, MT-Bench |
| **Human Evaluation** | Chatbot Arena (ELO), Red-Teaming |
| **Production Monitoring** | A/B testing, user feedback loops |

### Key Benchmarks

| Benchmark | What It Measures | SOTA (2025) |
|-----------|-----------------|-------------|
| **MMLU** | Multi-task knowledge (57 subjects) | ~90% |
| **GSM8K** | Grade-school math word problems | ~98% |
| **MATH** | Competition-level math | ~85% |
| **HumanEval** | Python code generation (Pass@1) | ~95% |
| **SWE-bench** | Real software engineering | ~50% |
| **Chatbot Arena** | Blind preference ranking | 1400+ ELO |

## Hallucination Taxonomy

```mermaid
graph TB
    subgraph Types["Types of Hallucination"]
        H1[Factual<br/>Wrong dates, names] --> H2[Reasoning<br/>Faulty logic]
        H2 --> H3[Instruction<br/>Ignores constraints]
        H3 --> H4[Context<br/>Contradicts prompt]
        H4 --> H5[Positional<br/>Lost in middle]
    end
    subgraph Detect["Detection"]
        D1[Self-Consistency]
        D2[Factual Grounding]
        D3[Calibration]
        D4[External Verifier]
    end
    subgraph Mitigate["Mitigation"]
        M1[RAG + Citations]
        M2[Constrained Decoding]
        M3[Prompt Engineering]
        M4[Uncertainty Prompting]
    end
    Types --> Detect --> Mitigate
```

**Links**: [[AI-ML/NLP/LLM/09 Models, Trends & Selection]] | [[AI-ML/NLP/LLM/07 RAG & Inference Optimization]] | [[AI-ML/NLP/LLM/05 Prompting Strategies]]
**See also**: [[LLM Evaluation and Benchmarks]] | [[LLM Alignment]]
