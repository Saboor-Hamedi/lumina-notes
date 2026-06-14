---
tags: [prompting, structure, format, persona]
---

# 06 — Structure & Format

## Separator Patterns

Use clear delimiters to help the LLM distinguish different parts of the input:

```python
prompt = """
<system>You are a medical Q&A assistant.</system>
<context>{documents}</context>
<question>{question}</question>
<instructions>- Answer based ONLY on context</instructions>
"""
```

Why separators matter: LLMs pay more attention to the start and end of prompts. Separators reduce context blending where the model confuses instruction with data.

## Role / Persona Prompting

```python
roles = {
    "senior_engineer": "You are a senior software engineer with 15 years at FAANG.",
    "science_teacher": "You are a high school science teacher who explains simply.",
    "socratic_tutor": "You answer questions with guiding questions.",
}
```

| Persona | When It Helps | When It Hurts |
|---------|--------------|---------------|
| Expert | Technical deep dives | Simple explanations (too verbose) |
| Teacher | Explanations, tutorials | Expert-level tasks (oversimplifies) |
| Skeptic | Fact-checking | Creative tasks (too negative) |

## Format Constraints

```python
# JSON mode
prompt = """
Respond in valid JSON with exactly these keys:
{
    "entities": [{"name": str, "type": str}],
    "summary": str,
    "sentiment": str
}
"""

# Schema-enforced (Pydantic + structured output)
from pydantic import BaseModel
class Extraction(BaseModel):
    entities: list[Entity]
    summary: str
    sentiment: str

response = client.beta.chat.completions.parse(
    model="gpt-4o", messages=[...], response_format=Extraction
)
```

**Links**: [[AI-ML/NLP/Advanced Prompting Techniques/05 Interaction & Tool-Use]] | [[AI-ML/NLP/Advanced Prompting Techniques/02 Reasoning & Logic]] | [[AI-ML/NLP/Advanced Prompting Techniques/08 Production Prompt & Pitfalls]]
**See also**: [[Prompt Engineering]], [[Prompt Engineering for RAG]]
