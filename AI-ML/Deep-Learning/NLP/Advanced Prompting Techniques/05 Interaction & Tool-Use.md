---
tags: [prompting, react, tool-use, function-calling]
---

# 05 — Interaction & Tool-Use

## ReAct (Reasoning + Acting)

Interleave reasoning traces with actions: think → act → observe → continue.

```
Thought: I need to find the population of Tokyo.
Action: search("Tokyo population 2026")
Observation: Tokyo population is approximately 14 million.
Thought: Now I need New York's population.
Action: search("New York population 2026")
Observation: New York population is approximately 8.5 million.
Thought: Tokyo (14M) is larger than New York (8.5M) by 5.5 million.
Answer: Tokyo has 5.5M more people than New York.
```

```python
def react_loop(llm, question, max_steps=10):
    messages = [{"role": "system", "content": "Reason step by step and call tools when needed."},
                {"role": "user", "content": question}]
    for step in range(max_steps):
        response = llm.generate(messages)
        if has_answer(response): return extract_answer(response)
        if has_action(response):
            action = parse_action(response)
            result = execute_tool(action["name"], action["args"])
            messages.append({"role": "user", "content": f"Observation: {result}"})
```

## Function Calling / Tool Use

Structured version of ReAct: outputs JSON function calls instead of free-form text.

```python
tools = [{"type": "function", "function": {
    "name": "get_weather",
    "parameters": {"type": "object", "properties": {"location": {"type": "string"}}}
}}]
response = client.chat.completions.create(model="gpt-4o", messages=[...], tools=tools)
```

| Pattern | Description |
|---------|-------------|
| **Tool call** | LLM outputs function name + args |
| **Multi-tool** | LLM calls multiple tools in one turn |
| **Tool chaining** | Output of one tool feeds another |
| **Conditional** | LLM decides tool based on prior result |

## Reflexion

Add explicit memory of past failures. When the model makes a mistake, it records the error and avoids similar mistakes in future attempts.

```
Attempt 1: ✗ — forgot to include comparison
Memory: "Forgot to compare both cities."
Attempt 2: ✓ — included comparison in final answer
```

**Links**: [[AI-ML/NLP/Advanced Prompting Techniques/04 Self-Improvement]] | [[AI-ML/NLP/Advanced Prompting Techniques/06 Structure & Format]] | [[AI-ML/NLP/Advanced Prompting Techniques/07 Selection Guide]]
**See also**: [[LLM Agents Framework]], [[Agentic RAG]]
