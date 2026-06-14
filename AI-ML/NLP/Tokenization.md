---
id: nlp-011-0000-0000-0000-000000000019
title: Tokenization
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781700000019
---
# Tokenization

**Links**: [[NLP Pipeline Design]] | [[Pre-training and Fine-tuning]] | [[BERT and Encoder Models]] | [[GPT and Decoder Models]] | [[Transformer Architecture]]

## What is Tokenization?

Tokenization splits text into smaller units (tokens) that the model can process. It's the first step in any NLP pipeline.

## Tokenization Methods

| Method | Example | Vocabulary | Handles Unknown |
|--------|---------|------------|-----------------|
| **Word-level** | "I love NLP" → ["I", "love", "NLP"] | 100k+ | No (UNK token) |
| **Character-level** | "cat" → ["c", "a", "t"] | ~100 | Yes |
| **Subword (BPE)** | "playing" → ["play", "ing"] | 30k-100k | Yes |
| **Subword (WordPiece)** | "playing" → ["play", "##ing"] | 30k | Yes |
| **Unigram** | Probability-based subword | 30k | Yes |
| **SentencePiece** | Language-agnostic subword | 32k-256k | Yes |

## Byte-Pair Encoding (BPE)

1. Start with individual characters
2. Count adjacent pairs
3. Merge the most frequent pair
4. Repeat until vocabulary size is reached

```
Initial: ["l", "o", "w", "e", "r", "l", "o", "w"]
Merge "l"+"o" → ["lo", "w", "e", "r", "lo", "w"]
Merge "lo"+"w" → ["low", "e", "r", "low"]
Merge "r"+"low" → ["low", "er", "low"]
```

## Hugging Face Tokenizer

```python
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")

tokens = tokenizer("Hello, how are you?")
print(tokens.input_ids)
# [101, 7592, 1010, 2129, 2024, 2017, 1029, 102]
#  [CLS] Hello  ,   how   are   you    ?  [SEP]

# Decode back
text = tokenizer.decode(tokens.input_ids)
# "[CLS] hello, how are you? [SEP]"
```

## Special Tokens

| Token | BERT | GPT | Meaning |
|-------|------|-----|---------|
| `[CLS]` | ✅ | ❌ | Start of sequence (classification) |
| `[SEP]` | ✅ | ❌ | Separator between sentences |
| `[PAD]` | ✅ | ✅ | Padding (aligns batch) |
| `[UNK]` | ✅ | ✅ | Unknown token |
| `[MASK]` | ✅ | ❌ | Masked token (MLM) |
| `<|endoftext|>` | ❌ | ✅ | End of sequence |

**Next**: [[Attention Mechanism]] — The key idea