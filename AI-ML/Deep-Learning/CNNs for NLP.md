---
id: nlp-020-0000-0000-0000-000000000028
title: CNNs for NLP
language: markdown
tags:
  - ai-ml
  - deep-learning
  - cnn
  - nlp
selection: null
isPinned: false
timestamp: 1781777997050
---
# CNNs for NLP

**Links**: [[Text Classification]] | [[RNNs and LSTMs]] | [[BERT and Encoder Models]] | [[NLP Pipeline Design]] | [[Word Embeddings]]

## Why CNNs for Text?

Convolutional Neural Networks (CNNs) can capture local patterns (n-grams) in text, regardless of position. They're fast and excel at pattern detection.

## Text as an Image Analogy

```
Image:    [pixel] [pixel] [pixel]
           filter detects local patterns (edges, textures)

Text:     [word]  [word]  [word]
           filter detects local patterns (trigrams, collocations)
```

## Architecture

```
Input:  "The cat sat on the mat"
           ↓
Word Embeddings (300d)
   ┌─┬─┬─┬─┬─┬─┐
   │w│w│w│w│w│w│
   └─┴─┴─┴─┴─┴─┘
           ↓
Convolutional filters (multiple sizes)
   ┌───┐   ┌─────┐   ┌───────┐
   │2-gram│ │3-gram│  │4-gram│
   └─────┘ └─────┘ └───────┘
           ↓
1-Max Pooling (capture strongest signal per filter)
           ↓
Concatenate → Fully Connected → Softmax
```

## TextCNN Implementation

```python
import torch.nn as nn

class TextCNN(nn.Module):
    def __init__(self, vocab_size, embed_dim, num_filters, filter_sizes):
        super().__init__()
        self.embedding = nn.Embedding(vocab_size, embed_dim)
        self.convs = nn.ModuleList([
            nn.Conv2d(1, num_filters, (fs, embed_dim))
            for fs in filter_sizes
        ])
        self.fc = nn.Linear(len(filter_sizes) * num_filters, num_classes)

    def forward(self, x):
        x = self.embedding(x).unsqueeze(1)  # (batch, 1, seq_len, embed_dim)
        x = [torch.relu(conv(x)).squeeze(3) for conv in self.convs]
        x = [torch.max_pool1d(c, c.size(2)).squeeze(2) for c in x]
        x = torch.cat(x, 1)
        return self.fc(x)
```

## Filter Sizes

| Filter Size | Captures | Analogy |
|-------------|----------|---------|
| 2 | Bigrams | "not good" |
| 3 | Trigrams | "was not good" |
| 4 | 4-grams | "was not very good" |
| 5 | 5-grams | "This was not very good" |

## Pros and Cons

| Pros | Cons |
|------|------|
| Very fast to train | Fixed context window (limited long-range) |
| Good at detecting key phrases | No sequential order beyond window |
| Few parameters | Position-invariant (loses word order nuance) |
| Parallel training | Not as good as Transformers for complex NLP |

**Next**: [[RNNs and LSTMs]] — Recurrent architectures
