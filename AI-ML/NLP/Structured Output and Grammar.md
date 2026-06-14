---
id: a1b2c3d4-1060-4000-8000-000000000060
title: Structured Output and Grammar
language: markdown
tags: [ai-ml, nlp]
selection: null
isPinned: false
timestamp: 1781500001060
---
# Structured Output and Grammar

Structured output constrains LLM generation to follow a specific schema — JSON, code, or any formal grammar — ensuring parseable, predictable results.

## Why Structured Output?

| Problem | Solution |
|---------|----------|
| Unparseable JSON | Schema-constrained generation |
| Malformed SQL | Grammar-guided decoding |
| Invalid code | Syntax constraint |
| Out-of-range values | Type/range constraints |
| Hallucinated keys | Schema-defined field names |

## JSON Mode (API-Level)

```python
# OpenAI JSON mode
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Extract: John is 30"}],
    response_format={"type": "json_object"}
)
# {"name": "John", "age": 30}  ← guaranteed JSON, but schema may vary

# Structured Outputs (OpenAI)
from pydantic import BaseModel

class Person(BaseModel):
    name: str
    age: int

response = client.beta.chat.completions.parse(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Extract: John is 30"}],
    response_format=Person
)
# Guaranteed: Person(name="John", age=30)
```

## Constrained Decoding

Instead of sampling from the full vocabulary, mask invalid tokens at each step:

```python
# Pseudo-code for JSON constraint
def get_valid_tokens(logits, state, schema):
    valid = set()
    if state.expecting_key:
        valid.add('"')  # must start with quote
    elif state.expecting_colon:
        valid.add(':')
    elif state.expecting_value:
        if schema.type == "string":
            valid.update(STRING_CHARS)
        elif schema.type == "number":
            valid.update(NUMBER_CHARS)
    # Mask logits to only valid tokens
    masked = mask_logits(logits, valid)
    return sample(masked)
```

## Libraries for Structured Output

| Library | Approach | Features |
|---------|----------|----------|
| Outlines | Regex/grammar + FSM | JSON, JSON Schema, Python types, CFG |
| LMQL | Declarative query language | Constraints + distribution control |
| Guidance | Template-based | Token-level masking, JSON |
| Instructor | Pydantic → JSON Schema | Retry, validation, streaming |
| JSONFormer | JSON Schema → FSM | Efficient JSON decoding |

## Outlines Example

```python
import outlines

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")

# Define schema
class Person:
    name: str
    age: int

# Generate constrained output
generator = outlines.generate.json(model, Person)
result = generator("Extract: John is 30 years old.")
# Person(name="John", age=30)
```

## Grammar-Guided Generation

```python
import outlines

# Generate SQL constrained by grammar
sql_grammar = """
?start: SELECT column FROM table WHERE condition
column: "name" | "age" | "email"
table: "users" | "orders" | "products"
condition: column OP value
OP: "=" | ">" | "<"
value: NUMBER | WORD
"""

generator = outlines.generate.cfg(model, sql_grammar)
sql = generator("Generate SQL to find users over 25")
# SELECT name FROM users WHERE age > 25
```

## Intermediate State Machine

```
State: ExpectingField
  Input: '"' → State: InFieldName
  Input: '}' → State: Done (if no more fields)

State: InFieldName
  Input: '"' → State: ExpectingColon
  Input: char → State: InFieldName

State: ExpectingColon
  Input: ':' → State: ExpectingValue

State: ExpectingValue
  Input: depends on schema type
```

## Retry + Validation Pattern

```python
from pydantic import BaseModel, ValidationError

class Recipe(BaseModel):
    name: str
    ingredients: list[str]
    steps: list[str]

def extract_recipe(text, max_retries=3):
    for i in range(max_retries):
        response = llm(f"Extract recipe as JSON:\n{text}", response_format="json")
        try:
            return Recipe.model_validate_json(response)
        except ValidationError as e:
            error_prompt = f"Fix JSON to match schema:\nError: {e}\nBad JSON: {response}"
            response = llm(error_prompt, response_format="json")
    raise ValueError("Failed after retries")
```

## Tradeoffs

| Approach | Correctness | Speed | Flexibility |
|----------|-------------|-------|-------------|
| JSON mode (API) | Moderately reliable | Fast | Less (schema varies) |
| Structured Outputs (API) | High | Fast | Schema-defined |
| Constrained decoding | Guaranteed | Slower | Highly flexible |
| Retry + validation | High | Variable | Schemas + custom logic |

## Use Cases

- **API integrations**: Guaranteed parseable JSON
- **Data extraction**: Schema-defined entity extraction
- **Code generation**: Syntax-safe code output
- **Database queries**: Valid SQL generation
- **UI components**: Type-safe component props
- **Form filling**: Validate against field types

**Links**: [[Prompt Engineering]] | [[Advanced Prompting Techniques]] | [[REST API Design]] | [[API Documentation]] | [[JSON Web Tokens]]
