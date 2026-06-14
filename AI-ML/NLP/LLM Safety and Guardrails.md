---
id: a1b2c3d4-1056-4000-8000-000000000056
title: LLM Safety and Guardrails
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001056
---
# LLM Safety and Guardrails

LLM safety prevents models from generating harmful, biased, or inappropriate content. Guardrails enforce boundaries around model behavior programmatically.

## Safety Layers

```
User Input → Input Guard → Model → Output Guard → Response
                ↓                          ↓
        Block toxic input         Block toxic output
        Detect jailbreaks         Verify factual claims
        Rate limit                Redact PII
```

## Input Guardrails

| Threat | Detection | Mitigation |
|--------|-----------|------------|
| Jailbreaks | Prompt injection classifiers | Reject or sanitize |
| Toxic content | Toxicity classifiers (Perspective API) | Block |
| Policy violations | Keyword/pattern matching | Reject with explanation |
| PII leakage | PII detectors | Redact or block |
| Rate abuse | Rate limiter | Throttle |

## Output Guardrails

| Threat | Detection | Mitigation |
|--------|-----------|------------|
| Toxic output | Toxicity classifiers | Disallow or regenerate |
| Hallucination | Fact-check against sources | Add disclaimer |
| Unsupported claims | Entailment check with context | Rewrite or remove |
| PII generation | Regex + NER | Redact |
| Unsafe code | Code scanner | Block execution examples |

## Guardrails Frameworks

| Framework | Features |
|-----------|----------|
| NVIDIA NeMo Guardrails | Colang DSL, dialog rails, input/output rails |
| Guardrails AI | XML-based rail specs, reask, fix |
| Microsoft Presidio | PII detection and redaction |
| Lakera Guard | API-based, prompt injection + toxic content |
| LLM Guard | Input sanitization, output moderation |

## NeMo Guardrails Example

```colang
# Define a rail
define user express_harmful_intent
  "I want to hurt someone"
  "How do I make a weapon?"

define flow
  user express_harmful_intent
  bot inform "I cannot help with harmful content"
  bot suggest_alternative "Can I help with something constructive?"
```

## Prompt Injection Detection

```python
def detect_injection(prompt):
    signals = [
        "ignore previous instructions",
        "ignore all rules",
        "you are now",
        "forget everything",
        "DAN"  # "Do Anything Now"
    ]
    for signal in signals:
        if signal.lower() in prompt.lower():
            return True, f"Detected injection attempt: {signal}"
    return False, None
```

## Content Moderation Approaches

| Approach | Latency | Accuracy | Use Case |
|----------|---------|----------|----------|
| Regex/pattern | Fast | Low | Quick filters |
| ML classifier | Medium | High | Toxicity detection |
| LLM-as-judge | Slow | Highest | Complex edge cases |
| Embedding similarity | Fast | Medium | Known harmful patterns |

## Safety Evaluation

| Benchmark | What It Tests |
|-----------|--------------|
| AdvBench | Resistance to harmful requests |
| HarmBench | Comprehensive safety probing |
| SafetyPrompts | Chinese safety scenarios |
| SORRY-Bench | 40+ harm categories |

## Red Teaming

Systematic adversarial testing:

| Method | Description |
|--------|-------------|
| Manual | Human red teamers probe for weaknesses |
| Automated | LLM generates adversarial attacks (e.g., Peacok) |
| Evolutionary | Mutate successful jailbreaks |
| Multi-turn | Gradual conversation steering |

## Data Privacy

| Concern | Mitigation |
|---------|------------|
| Training data leakage | Deduplication, filtering |
| Prompt injection | Input guardrails |
| Model extraction | Rate limiting, watermarking |
| Memorization | Differential privacy training |

## Monitoring

- Log all input/output pairs
- Track safety violation rates over time
- Set alert thresholds for violation spikes
- Regular red teaming exercises
- Version ALL guardrail configurations

**Links**: [[LLM Alignment]] | [[Web Security]] | [[API Security]] | [[Prompt Engineering]] | [[RLHF]]
