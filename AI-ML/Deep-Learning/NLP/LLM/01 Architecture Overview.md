---
tags: [nlp, llm, architecture, transformer]
---

# 01 — Architecture Overview

## What is an LLM?

A **Large Language Model (LLM)** is a neural network trained on massive text corpora to understand and generate human-like text. LLMs use the **transformer architecture** and are trained via **next-token prediction**.

## Architecture Variants

```mermaid
graph LR
    subgraph EncoderOnly["Encoder-Only<br/>BERT, RoBERTa"]
        E1[Input] --> E2[Bidirectional Self-Attention]
        E2 --> E3[Understanding<br/>Classification, NER, QA]
    end
    subgraph DecoderOnly["Decoder-Only<br/>GPT, LLaMA, Claude"]
        D1[Input] --> D2[Causal Self-Attention]
        D2 --> D3[Generation<br/>Chat, Code, Writing]
    end
    subgraph EncDec["Encoder-Decoder<br/>T5, BART"]
        ED1[Input] --> ED2[Encoder<br/>Bidirectional]
        ED2 --> ED3[Decoder / Cross-Attention]
        ED3 --> ED4[Seq2Seq<br/>Translation, Summarization]
    end
```

## Core Architecture

```mermaid
graph TB
    subgraph Input["Input Processing"]
        TEXT["Raw Text"] --> TOK[Tokenization]
        TOK --> EMB[Embedding Layer]
        EMB --> POS[Positional Encoding]
    end
    subgraph Transformer["Transformer Blocks (×N)"]
        POS --> MHA[Multi-Head Self-Attention]
        MHA --> ADD1[Add & Norm]
        ADD1 --> FF[Feed-Forward Network]
        FF --> ADD2[Add & Norm]
        ADD2 --> MHA
    end
    subgraph Output["Output Generation"]
        ADD2 --> LM[LM Head / Unembedding]
        LM --> SOFT[Softmax]
        SOFT --> PROBS[Probability Distribution]
        PROBS --> SAMPLE[Sample / Argmax]
        SAMPLE --> NEXT[Next Token]
    end
    NEXT -.->|Autoregressive Loop| TOK
```

## A Single Transformer Block

```mermaid
graph LR
    subgraph Block["Transformer Block"]
        X[Input] --> LN1[Layer Norm]
        LN1 --> ATT[Multi-Head Attention]
        X -.->|Residual| R1[+]
        ATT --> R1
        R1 --> LN2[Layer Norm]
        LN2 --> FC[Feed-Forward]
        R1 -.->|Residual| R2[+]
        FC --> R2
        R2 --> OUT[Output]
    end
```

## Inside Self-Attention

| Step | Operation | Purpose |
|------|-----------|---------|
| 1 | Q, K, V = Linear(X) | Project input into query, key, value spaces |
| 2 | Scores = Q × K^T | Compute pairwise attention scores |
| 3 | Scale by √d_k | Prevent softmax saturation |
| 4 | Softmax (normalize) | Convert to attention weights |
| 5 | Output = Softmax × V | Weighted sum of values |

## Multi-Head Attention

| Head | Focuses On |
|------|------------|
| Head 1 | Syntactic relationships (subject-verb agreement) |
| Head 2 | Positional / distance patterns |
| Head 3 | Semantic similarity (synonyms, antonyms) |
| Head 4 | Coreference resolution (pronouns → nouns) |
| Head 5 | Dependency parsing |
| Head 6+ | Mixed / learned patterns |

## Attention Masks

- **Causal Mask** (Decoder-Only): Token can only attend to itself and previous tokens (GPT, LLaMA)
- **Bidirectional Mask** (Encoder-Only): Token attends to all tokens (BERT)
- **Cross-Attention Mask** (Encoder-Decoder): Decoder tokens attend to all encoder tokens (T5, BART)

**Links**: [[AI-ML/NLP/LLM/02 Tokenization & Generation]] | [[AI-ML/NLP/LLM/03 Training & Data]] | [[AI-ML/NLP/LLM/04 Fine-Tuning]]
**See also**: [[Transformer Architecture]] | [[Attention Mechanism]]
