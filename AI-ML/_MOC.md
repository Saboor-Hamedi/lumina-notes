---
id: moc-aiml-0000-0000-0000-000000000001
title: AI / ML — Map of Content
language: markdown
tags: [moc, index, ai-ml]
isPinned: true
timestamp: 1781700000001
---

# 🤖 AI / ML — Map of Content

Artificial intelligence and machine learning are reshaping how software understands, generates, and interacts with data. This folder covers deep learning (transformers, attention, training), NLP (tokenization, LLMs, prompt engineering, alignment), and retrieval-augmented generation (RAG architecture, vector databases, embedding models, evaluation). Start with [[AI-ML/Deep-Learning/Attention Mechanism]] for the foundational concept behind modern AI.

```mermaid
graph TD
  AIML["AI / ML"] --> DL["Deep Learning"]
  AIML --> NLP["NLP / LLMs"]
  AIML --> RAG["RAG"]
  AIML --> ML["Machine Learning"]

  DL --> TF["Transformer Architecture"]
  DL --> SA["Self-Attention"]
  DL --> MHA["Multi-Head Attention"]
  DL --> PE["Positional Encoding"]
  DL --> BERT["BERT & Encoder Models"]
  DL --> GPT["GPT & Decoder Models"]
  DL --> ATT["Attention Mechanism"]
  DL --> PT["Pre-training & Fine-tuning"]
  DL --> LORA["LoRA & PEFT"]
  DL --> MOE["Mixture of Experts"]
  DL --> SL["Scaling Laws"]
  DL --> FA["Flash Attention"]
  DL --> KV["KV Cache & Inference"]
  DL --> SPD["Speculative Decoding"]
  DL --> LCT["Long Context Transformers"]
  DL --> RNN["RNNs & LSTMs"]
  DL --> CNN["CNNs for NLP"]
  DL --> S2S["Seq2Seq Models"]
  DL --> ED["Encoder-Decoder"]
  DL --> MD["Model Distillation"]
  DL --> CV["Computer Vision"]

  NLP --> TOK["Tokenization"]
  NLP --> TEM["Text Embedding Models"]
  NLP --> PE2["Prompt Engineering"]
  NLP --> APT["Advanced Prompting"]
  NLP --> CWS["Context Window Strategies"]
  NLP --> ALIGN["LLM Alignment"]
  NLP --> SAFE["LLM Safety & Guardrails"]
  NLP --> EVAL["LLM Evaluation"]
  NLP --> TUF["Tool Use & Function Calling"]
  NLP --> SOG["Structured Output & Grammar"]
  NLP --> QNT["Quantization for LLMs"]
  NLP --> IO["Inference Optimization"]
  NLP --> MT["Machine Translation"]
  NLP --> SA2["Sentiment Analysis"]
  NLP --> TC["Text Classification"]
  NLP --> NER["Named Entity Recognition"]
  NLP --> NLPIP["NLP Pipeline Design"]
  NLP --> AGENTS["LLM Agents Framework"]

  RAG --> RAGARCH["RAG Architecture"]
  RAG --> CHUNK["Chunking Strategies"]
  RAG --> EMB["Embedding Models for RAG"]
  RAG --> VDB["Vector Databases for RAG"]
  RAG --> RET["Retrieval Strategies"]
  RAG --> RERANK["Reranking"]
  RAG --> PER["Prompt Engineering for RAG"]
  RAG --> ADV["Advanced RAG Patterns"]
  RAG --> ARAG["Agentic RAG"]
  RAG --> GRAG["Graph RAG"]
  RAG --> MMRAG["Multi-Modal RAG"]
  RAG --> HYBRID["Hybrid Search for RAG"]
  RAG --> OPT["RAG Pipeline Optimization"]
  RAG --> MET["RAG Evaluation Metrics"]
  RAG --> RVF["RAG vs Fine-tuning"]

  ML --> MLOps["MLOps"]
  ML --> FS["Feature Stores"]
  ML --> MM["Model Monitoring"]
  ML --> REAI["Responsible & Ethical AI"]
  ML --> SDG["Synthetic Data Generation"]
  ML --> CI["Causal Inference"]
  ML --> GNN["Graph Neural Networks"]
  ML --> IT["Information Theory"]
  ML --> NAS["Neural Architecture Search"]
  ML --> TS["Time Series Analysis"]
  ML --> AD["Anomaly Detection"]
  ML --> RS["Recommender Systems"]
  ML --> CLUST["Clustering Algorithms"]
  ML --> DT["Decision Trees & Random Forests"]
  ML --> GB["Gradient Boosting"]
  ML --> EM["Ensemble Methods"]
  ML --> HT["Hyperparameter Tuning"]
  ML --> DR["Dimensionality Reduction"]
  ML --> AL["Active Learning"]
  ML --> RL["Reinforcement Learning"]
  ML --> DAN["Data Augmentation for NLP"]
```

## Sub-Areas

| Area | Notes | Description |
|------|-------|-------------|
| [[AI-ML/Deep-Learning/_MOC\|Deep Learning]] | 22 | Transformers, attention, architectures |
| [[AI-ML/NLP/_MOC\|NLP / LLMs]] | 19 | Tokenization, prompting, alignment, agents |
| [[AI-ML/RAG/_MOC\|RAG]] | 16 | Retrieval-Augmented Generation |
| [[AI-ML/Deep-Learning/Machine-Learning/_MOC\|Machine Learning]] | 21 | Classical ML, MLOps, ethics |

## Cross-Domain Links

- [[AI-ML/Deep-Learning/Machine-Learning/MLOps]] → [[DevOps/CI-CD/CI CD Pipelines]], [[DevOps/Containers/Docker Containers]], [[DevOps/Containers/Kubernetes Basics]]
- [[AI-ML/RAG/Vector Databases for RAG]] → [[System-Design/Databases/Search Engines]], [[System-Design/Databases/Elasticsearch Deep Dive]]
- [[AI-ML/Deep-Learning/Machine-Learning/Model Monitoring in Production]] → [[DevOps/Monitoring/Monitoring and Observability]], [[DevOps/Monitoring/Prometheus and Monitoring Systems]]
- [[AI-ML/Deep-Learning/Pre-training and Fine-tuning]] → [[AI-ML/RAG/RAG vs Fine-tuning]]
- [[AI-ML/NLP/LLM Agents Framework]] → [[Security/Web Security]], [[DevOps/REST API Design]]
