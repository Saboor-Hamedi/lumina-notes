---
id: a1b2c3d4-1044-4000-8000-000000000044
title: Agentic RAG
language: markdown
tags: [ai-ml, rag, agentic]
selection: null
isPinned: false
timestamp: 1781500001044
---
# Agentic RAG

Agentic RAG gives LLM agents autonomous access to retrieval tools — deciding when to retrieve, what to retrieve, and how to use the results to accomplish a task.

## What Makes it Agentic?

| Traditional RAG | Agentic RAG |
|-----------------|-------------|
| Single retrieve → generate | Iterative retrieval loop |
| Fixed top-k chunks | Dynamic query refinement |
| One-shot context | Multi-turn reasoning |
| Passive retrieval | Active decision to search |

## Architecture

```
User Query → LLM Agent
               │
               ├── Tool: Search (vector DB)
               ├── Tool: Web Search
               ├── Tool: Code Interpreter
               ├── Tool: Database Query
               └── Tool: Calculator
                    ↓
        Agent decides: sufficient info → answer
        Agent decides: needs more → refine + search again
```

## Tool Use Pattern

```python
tools = [
    {
        "type": "function",
        "function": {
            "name": "search_knowledge_base",
            "description": "Search technical documentation",
            "parameters": {
                "type": "object",
                "properties": {
                    "query": {"type": "string"},
                    "filters": {"type": "object"}
                },
                "required": ["query"]
            }
        }
    }
]

response = client.chat.completions.create(
    model="gpt-4",
    messages=messages,
    tools=tools,
    tool_choice="auto"
)
```

## Retrieval Strategies an Agent Can Use

| Strategy | Agent Action |
|----------|-------------|
| Query decomposition | Break complex question into sub-queries |
| Iterative refinement | Search, read, refine search terms |
| Multi-hop retrieval | Use result A to craft query B |
| Comparative search | Search multiple sources for comparison |
| Time-aware retrieval | Filter by recency, search historical versions |

## ReAct Loop (Reasoning + Acting)

```
Thought: I need to find the API rate limit for GPT-4.
Action: search_knowledge_base(query="GPT-4 API rate limits")
Observation: "GPT-4: 10K RPM for tier 5"
Thought: That answers the question. I should also check pricing.
Action: search_knowledge_base(query="GPT-4 API pricing")
Observation: "$30 per 1M input tokens"
Final: The GPT-4 API allows 10K requests per minute at tier 5, costing $30 per 1M input tokens.
```

## Memory in Agentic RAG

| Memory Type | Duration | Scope |
|-------------|----------|-------|
| Ephemeral | Single turn | Current query context |
| Working | Multi-turn | Conversation history |
| Long-term | Persistent | User preferences, past retrievals |

## Guardrails

- **Max iterations**: Prevent infinite loops
- **Token budget**: Limit total context consumption
- **Source tracking**: Always cite retrieved chunks
- **Confidence threshold**: Don't answer if retrieval quality is low

## Frameworks

| Framework | Language | Features |
|-----------|----------|----------|
| LangGraph | Python | Stateful graphs, human-in-loop |
| AutoGen | Python | Multi-agent conversations |
| CrewAI | Python | Role-based agent teams |
| Semantic Kernel | C#/Python | Microsoft ecosystem |
| OpenAI Assistants | API | Hosted, file search built-in |

## Use Cases

| Use Case | Why Agentic? |
|----------|--------------|
| Research assistant | Multi-source synthesis, follow-up queries |
| Code migration | Read multiple files, understand patterns |
| Customer support | Access KB, check account, escalate |
| Data analysis | Query DB, transform, visualize |

**Links**: [[LLM Agents Framework]] | [[Advanced RAG Patterns]] | [[Tool Use and Function Calling]] | [[RAG Architecture]] | [[Prompt Engineering for RAG]]
