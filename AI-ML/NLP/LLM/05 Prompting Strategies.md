---
tags: [nlp, llm, prompting, prompt-engineering]
---

# 05 — Prompting Strategies

## System Prompt Design

```mermaid
graph TB
    subgraph Components["System Prompt Components"]
        ROLE[Role Definition] --> RULES[Behavioral Rules]
        RULES --> FORMAT[Output Format]
        FORMAT --> CONST[Constraints]
        CONST --> CTX[Context]
        CTX --> EXAMPLES[Examples]
    end
```

### Best Practices
- Be specific and explicit
- Use positive instructions ("Do X" not "Don't do Y")
- Put critical rules at start
- Separate system vs user clearly

### System Prompt Patterns

| Pattern | Effect |
|---------|--------|
| **Persona** | Aligns expertise level |
| **Step-by-Step** | Improves reasoning |
| **Format Enforcer** | Structured output |
| **Few-Shot in System** | Sets pattern, hidden from user |

## Prompting Strategies

```mermaid
graph TB
    subgraph ZeroShot["Zero-Shot"]
        Z1[Instruction] --> Z2[LLM] --> Z3[Output]
    end
    subgraph FewShot["Few-Shot"]
        F1[Examples + Query] --> F2[LLM] --> F3[Output]
    end
    subgraph CoT["Chain-of-Thought"]
        C1[Question] --> C2["Let's think step-by-step..."]
        C2 --> C3[Answer]
    end
    subgraph TreeOfThought["Tree-of-Thought"]
        T1[Problem] --> T2[Branch A] & T3[Branch B] & T4[Branch C]
        T2 & T3 & T4 --> T5[Evaluate + Best Path]
    end
```

| Strategy | When to Use | Quality Gain |
|----------|-------------|--------------|
| **Zero-shot** | Simple, well-known tasks | Baseline |
| **Few-shot** | New format, complex tasks | +10-30% |
| **Chain-of-Thought** | Math, logic, reasoning | +15-40% |
| **Tree-of-Thought** | Planning, puzzles | +20-50% |
| **ReAct** | Multi-step tool use | +30% on agent tasks |
| **Self-Consistency** | High-stakes decisions | +5-15% |

**Links**: [[AI-ML/NLP/LLM/06 Tool Use & Multi-Agent]] | [[AI-ML/NLP/LLM/02 Tokenization & Generation]] | [[AI-ML/NLP/LLM/07 RAG & Inference Optimization]]
**See also**: [[Prompt Engineering]] | [[Advanced Prompting Techniques]]
