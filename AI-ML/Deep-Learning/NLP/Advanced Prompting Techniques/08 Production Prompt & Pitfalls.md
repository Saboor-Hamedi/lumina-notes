---
tags: [prompting, production, pitfalls]
---

# 08 — Production Prompt & Pitfalls

## Production Prompt Template

```python
system_prompt = """
You are a senior data analyst assistant. Follow this process for every query:

1. UNDERSTAND: Rephrase the question in your own words
2. PLAN: Identify what data/steps are needed
3. EXECUTE: Use tools (search, calculate, code) when needed
4. VERIFY: Check your answer for consistency
5. RESPOND: Provide the final answer with reasoning

FORMAT:
Reasoning: <step-by-step thinking>
Answer: <concise answer>
Confidence: <high/medium/low>
Sources: <if any>

CONSTRAINTS:
- If uncertain, say "I don't know"
- Cite all sources
- Use code for any numerical computation
"""

def advanced_query(llm, question, context=None):
    prompt = f"{system_prompt}\n\nQuestion: {question}"
    if context: prompt += f"\nContext:\n{context}"
    response = llm.generate(prompt, temperature=0.3)
    if needs_verification(response):
        alternatives = [llm.generate(prompt, temperature=0.5) for _ in range(3)]
        if not is_consistent(extract_answer(response), alternatives):
            response = llm.generate(prompt + "\n\nRe-check your reasoning carefully.")
            response += "\n\nNote: Verified via self-consistency."
    return response
```

## Common Pitfalls & Antipatterns

| Pitfall | Fix |
|---------|-----|
| **Over-prompting** (contradictory instructions) | Keep focused, one task at a time |
| **Under-specifying** (wrong assumptions) | Be explicit about format, scope |
| **Priming bias** (repeats patterns) | Diverse examples, counter-examples |
| **Over-confidence** | Force confidence expression, use CoT |
| **Context stuffing** (lost-in-middle) | Critical instructions at start AND end |
| **Negation confusion** ("Don't mention X" → mentions X) | Frame positively: "Focus on Y" |
| **Format drift** (output degrades over convos) | Refresh system prompt periodically |
| **Circular reasoning** (CoT loops) | Limit steps, force "I don't know" |

**Links**: [[AI-ML/NLP/Advanced Prompting Techniques/07 Selection Guide]] | [[AI-ML/NLP/Advanced Prompting Techniques/09 Learning Progression & Quick Reference]] | [[AI-ML/NLP/Advanced Prompting Techniques/06 Structure & Format]]
**See also**: [[Prompt Engineering]], [[Prompt Engineering for RAG]]
