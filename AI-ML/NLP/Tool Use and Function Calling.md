---
id: a1b2c3d4-1057-4000-8000-000000000057
title: Tool Use and Function Calling
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001057
---
# Tool Use and Function Calling

Tool use allows LLMs to interact with external systems — databases, APIs, calculators, search engines — extending their capabilities beyond text generation.

## Why Tool Use?

| Limitation | Tool Solution |
|------------|--------------|
| No access to real-time data | Web search tool |
| Can't perform math reliably | Calculator tool |
| No persistent memory | Vector DB / SQL tool |
| Can't take actions | API invocation tool |
| No code execution | Code interpreter tool |

## Function Calling API

```python
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "Get current weather for a city",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {"type": "string", "description": "City name"},
                    "units": {"type": "string", "enum": ["celsius", "fahrenheit"]}
                },
                "required": ["city"]
            }
        }
    }
]

response = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "What's the weather in Tokyo?"}],
    tools=tools,
    tool_choice="auto"
)

if response.choices[0].message.tool_calls:
    # Execute function with parsed arguments
    for call in response.choices[0].message.tool_calls:
        result = execute_function(call.function.name, json.loads(call.function.arguments))
)
```

## Tool Call Flow

```
User: "Book a flight to NYC on June 20"

1. LLM → Tool call: search_flights(destination="NYC", date="2025-06-20")
2. System → Execute search_flights, return results
3. LLM → Tool call: search_hotels(city="NYC", dates=["2025-06-20", "2025-06-25"])
4. System → Execute search_hotels, return results
5. LLM → Final: "I found these flights and hotels..."
```

## Tool Types

| Type | Description | Examples |
|------|-------------|----------|
| Retrieval | Read from external sources | Vector DB, web search, SQL |
| Computation | Perform calculations | Calculator, code interpreter |
| Action | Modify external state | Send email, create ticket |
| Perception | Sense the environment | Camera, sensors, file reader |

## Structured Tool Definitions

```python
from pydantic import BaseModel

class WeatherInput(BaseModel):
    city: str
    units: str = "celsius"

def get_weather(params: WeatherInput) -> dict:
    """Get current weather for a city"""
    return api.fetch_weather(params.city, params.units)

# Register tool with schema
tool_registry.register(
    name="get_weather",
    fn=get_weather,
    schema=WeatherInput,
    description="Get current weather for a city"
)
```

## Parallel Tool Calls

Modern LLMs can call multiple tools in one response:

```python
# Single response can have multiple tool_calls
response.tool_calls = [
    ToolCall(name="search_flights", args={...}),
    ToolCall(name="search_hotels", args={...})
]
# All executed in parallel
```

## Common Tool Patterns

| Pattern | Description |
|---------|-------------|
| Single tool | One tool call per turn |
| Parallel tools | Independent tools called simultaneously |
| Sequential tools | Output of one tool feeds into next |
| Conditional tools | Tool selected based on context |
| Loop | Tool calls until task complete |

## Error Handling

```python
def safe_tool_call(tool_fn, args, max_retries=2):
    for attempt in range(max_retries):
        try:
            result = tool_fn(**args)
            return {"success": True, "result": result}
        except APIError as e:
            if attempt < max_retries - 1:
                time.sleep(1)
            else:
                return {"success": False, "error": str(e)}
```

## Security Considerations

| Risk | Mitigation |
|------|------------|
| Prompt injection via tool output | Sanitize results, limit tool scope |
| Unauthorized tool access | Permission system per tool |
| Infinite loops | Max iterations limit |
| Resource exhaustion | Timeout + rate limits |
| Data exfiltration | Restrict tool outputs, audit logs |

**Links**: [[LLM Agents Framework]] | [[Agentic RAG]] | [[REST API Design]] | [[API Security]] | [[JSON Web Tokens]]
