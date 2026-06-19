---
tags: [nlp, llm, agents, tool-use, multi-agent]
---

# 06 — Tool Use & Multi-Agent

## Tool Use & Function Calling

```mermaid
sequenceDiagram
    participant U as User
    participant LLM as LLM
    participant T as External Tool
    U->>LLM: "What's the weather in Tokyo?"
    LLM->>LLM: Decide: needs tool call
    LLM->>T: get_weather(city="Tokyo")
    T-->>LLM: {"temperature": 22, "condition": "sunny"}
    LLM->>LLM: Generate response
    LLM->>U: "22°C and sunny!"
```

### Tool Types

| Tool Type | Examples |
|-----------|----------|
| **Information Retrieval** | Web search, vector DB, SQL |
| **Code Execution** | Python interpreter, bash |
| **APIs** | Calendar, email, Slack |
| **Computation** | Calculator, unit conversion |
| **Multimodal** | Image generation, TTS |

## Multi-Agent Orchestration

```mermaid
graph TB
    subgraph Supervisor["Supervisor Agent"]
        S[Orchestrator<br/>Task decomposition<br/>Agent assignment]
    end
    subgraph Workers["Worker Agents"]
        W1[Researcher]
        W2[Coder]
        W3[Reviewer]
        W4[Writer]
    end
    subgraph Tools["Shared Tool Pool"]
        T1[Web Search]
        T2[Code Interpreter]
        T3[Vector Database]
        T4[File System]
    end
    S --> W1 & W2 & W3 & W4
    W1 --> T1 & T3
    W2 --> T2 & T4
    W3 --> T2
    W4 --> T4
    W1 & W2 & W3 & W4 -.->|Handoff| S
```

**Links**: [[AI-ML/NLP/LLM/05 Prompting Strategies]] | [[AI-ML/NLP/LLM/07 RAG & Inference Optimization]] | [[AI-ML/NLP/LLM/08 Safety, Evaluation & Hallucination]]
**See also**: [[LLM Alignment]] | [[Advanced Prompting Techniques]]
