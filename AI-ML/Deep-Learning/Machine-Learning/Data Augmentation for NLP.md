---
id: nlp-022-0000-0000-0000-000000000044
title: Data Augmentation for NLP
language: markdown
tags: '
selection: null
isPinned: false
timestamp: 1781800000022
---
# Data Augmentation for NLP

**Links**: [[NLP Pipeline Design]] | [[Pre-training and Fine-tuning]] | [[Text Classification]] | [[Word Embeddings]] | [[Named Entity Recognition]]

## What is Data Augmentation?

Data augmentation creates synthetic training examples from existing data to improve model robustness and reduce overfitting.

## Techniques

### 1. Synonym Replacement

Replace words with their synonyms while preserving meaning.

```python
from nltk.corpus import wordnet

def synonym_replace(text, p=0.3):
    words = text.split()
    new_words = []
    for w in words:
        if random.random() < p:
            synonyms = wordnet.synsets(w)
            if synonyms:
                w = random.choice(synonyms).lemmas()[0].name()
        new_words.append(w)
    return " ".join(new_words)
```

### 2. Back Translation

Translate text to another language and back.

```
English → French → English
"The cat sat on the mat" → "Le chat s'est assis sur le tapis" → "The cat sat down on the rug"
```

### 3. Random Insertion/Deletion/Swap

| Operation | Description |
|-----------|-------------|
| Insert | Add random word at random position |
| Delete | Remove random word |
| Swap | Swap two adjacent words |

### 4. EDA (Easy Data Augmentation)

Combination of synonym replacement, random insertion, random swap, and random deletion. Typically 10-50% of words modified.

### 5. LLM-Based Generation

```python
prompt = f"Generate a paraphrased version of: {original_text}"
augmented = llm.generate(prompt)
```

## When to Augment

- Small datasets (< 10k examples)
- Imbalanced classes
- Domain-specific data with limited sources
- Improving robustness to typos and variations

## Cautions

- Don't change the label (preserve meaning)
- Avoid introducing artifacts the model can learn
- Validate augmented data quality
- Some augmentations hurt more than help

**Next**: [[Active Learning]] — Smart data labeling