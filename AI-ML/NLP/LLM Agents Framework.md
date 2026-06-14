---
id: 4126307f-622c-491f-94b6-b34302eec8fb
title: LLM Agents Framework
language: markdown
tags: ''
selection: null
isPinned: true
timestamp: 1780933031634
---
# LLM Agents Framework

LLM agents are autonomous programs that use large language models to reason, plan, and execute tasks. They combine LLMs with tools, memory, and orchestration loops.

## Architecture

```
User Input → LLM (reasoning) → Tool Selection → Execution → Result → LLM (reflection)
```

## Core Components

- **LLM Core**: The reasoning engine (GPT-4, Claude, Llama)
- **Tools**: APIs, code interpreters, database connectors, web search
- **Memory**: Short-term (conversation context) + Long-term (vector stores)
- **Planner**: Decomposes complex tasks into sub-steps

## Popular Frameworks

| Framework | Description |
|-----------|-------------|
| LangGraph | Stateful agent orchestration |
| CrewAI | Multi-agent collaboration |
| AutoGPT | Autonomous goal-driven agents |
| Semantic Kernel | Microsoft's AI orchestration |

**Links**: [[Multi-Agent Orchestration]] | [[Tool Use and Function Calling]] | [[Advanced RAG Patterns]] | [[Agentic RAG]] | [[RAG Architecture]] | [[NLP Pipeline Design]] | [[Text Embedding Models]] | [[Programming Resources]]

**See also**: [[Microservices Architecture]], [[Event-Driven Architecture]], [[LLM Safety and Guardrails]], [[LLMOps and AI Observability]]
