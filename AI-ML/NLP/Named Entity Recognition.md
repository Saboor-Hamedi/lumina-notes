---
id: nlp-013-0000-0000-0000-000000000021
title: Named Entity Recognition (NER)
language: markdown
tags: [ai-ml, nlp, ner]
selection: null
isPinned: false
timestamp: 1781700000021
---
# Named Entity Recognition (NER)

**Links**: [[NLP Pipeline Design]] | [[BERT and Encoder Models]] | [[Tokenization]] | [[Sequence-to-Sequence Models]] | [[Pre-training and Fine-tuning]]

## What is NER?

NER identifies and classifies named entities in text into predefined categories (person, organization, location, etc.).

## Entity Types

| Type | Tag | Example |
|------|-----|---------|
| Person | PER | "**Alice** works here" |
| Organization | ORG | "**Google** announced..." |
| Location | LOC | "She lives in **Paris**" |
| Date | DATE | "Born **January 5, 1990**" |
| Money | MONEY | "Costs **$50 million**" |
| Time | TIME | "At **3 PM** sharp" |

## Tagging Schemes

| Scheme | "New York City" | Description |
|--------|-----------------|-------------|
| **IOB2** | B-LOC I-LOC I-LOC | Begin/Inside/Outside |
| **IOBES** | B-LOC I-LOC E-LOC | B/I/O/E/S (single) |

## BERT for NER

```python
from transformers import AutoTokenizer, AutoModelForTokenClassification
import torch

model = AutoModelForTokenClassification.from_pretrained(
    "dbmdz/bert-large-cased-finetuned-conll03-english"
)
tokenizer = AutoTokenizer.from_pretrained("bert-base-cased")

text = "Apple is looking at buying U.K. startup for $1 billion"
inputs = tokenizer(text, return_tensors="pt")

with torch.no_grad():
    outputs = model(**inputs)
    predictions = torch.argmax(outputs.logits, dim=2)

# Map back to tokens
tokens = tokenizer.convert_ids_to_tokens(inputs.input_ids[0])
labels = [model.config.id2label[p.item()] for p in predictions[0]]

for token, label in zip(tokens, labels):
    if label != "O":
        print(f"{token}: {label}")
```

## Applications

- **Information extraction**: Extract structured data from documents
- **Search**: Entity-aware indexing
- **Recommendation**: Detect entities in user queries
- **Compliance**: Identify PII, financial entities
- **Knowledge graphs**: Entity linking and disambiguation

## Evaluation

| Metric | Description |
|--------|-------------|
| **Precision** | % of predictions that are correct |
| **Recall** | % of entities found |
| **F1** | Harmonic mean of precision and recall |
| **Strict F1** | Exact boundary + type match |
| **Relaxed F1** | Partial boundary match allowed |

**Next**: [[Sequence-to-Sequence Models]] — Encoder-decoder architectures