---
id: nlp-017-0000-0000-0000-000000000025
title: Machine Translation
language: markdown
tags: [ai-ml, nlp, machine-translation]
selection: null
isPinned: false
timestamp: 1781800000003
---
# Machine Translation

**Links**: [[Sequence-to-Sequence Models]] | [[Attention Mechanism]] | [[Transformer Architecture]] | [[Pre-training and Fine-tuning]] | [[NLP Pipeline Design]]

## What is Machine Translation?

Machine translation (MT) automatically converts text from one language to another while preserving meaning.

## Evolution of MT

| Era | Approach | Example |
|-----|----------|---------|
| 1950s | Rule-based | Hand-crafted grammar rules |
| 1990s | Statistical (SMT) | Phrase tables + language models |
| 2014 | Neural (NMT) | Seq2Seq with attention |
| 2017 | Transformer | Parallel training, better quality |
| 2023 | LLM-based | GPT-4, Claude zero-shot translation |

## Transformer NMT Pipeline

```
Source: "Le chat est sur le tapis"
    ↓
[BPE Tokenization]
    ↓
[Transformer Encoder] → Contextual representations
    ↓
[Transformer Decoder] (autoregressive)
    ↓
[BPE Detokenization]
    ↓
Target: "The cat is on the mat"
```

## Evaluation Metrics

| Metric | What | How |
|--------|------|-----|
| **BLEU** | N-gram overlap | 0-100, higher = better |
| **TER** | Edit distance | Lower = better |
| **COMET** | Neural quality score | Trained on human ratings |
| **chrF** | Character n-gram F-score | Good for morphologically rich |

```python
from sacrebleu import corpus_bleu

refs = [["The cat is on the mat"]]
hyps = ["The cat is on the mat"]
bleu = corpus_bleu(hyps, refs)
print(bleu.score)  # 100.0 (exact match)
```

## Challenges

| Challenge | Example |
|-----------|---------|
| **Ambiguity** | "bank" (river vs financial) |
| **Idioms** | "It's raining cats and dogs" |
| **Gender** | Translator gender in languages like French |
| **Low resource** | Languages with little training data |
| **Domains** | Medical vs legal vs casual |

**Next**: [[Text Summarization]] — Condensing long documents