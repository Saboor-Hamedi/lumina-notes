---
id: rag-002-0000-0000-0000-000000000002
title: Chunking Strategies
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781700000002
---
# Chunking Strategies

**Links**: [[RAG Architecture]] | [[Text Embedding Models]] | [[Vector Databases for RAG]] | [[Retrieval Strategies]] | [[Reranking]]

## Why Chunk?

Documents must be split into smaller pieces (chunks) before embedding and indexing. The chunk size and strategy directly impact retrieval quality.

## Chunking Methods

| Strategy | How It Works | Best For |
|----------|-------------|----------|
| **Fixed-size** | Split every N characters/tokens with overlap | Simple documents |
| **Recursive** | Split on boundaries (\n\n → \n → . → space) | General text |
| **Semantic** | Split at topic boundaries using embeddings | Long articles |
| **Document-based** | Use natural sections (paragraphs, headings) | Structured docs |
| **Sentence-based** | Split on sentence boundaries | Dense information |
| **Agentic** | LLM decides where to split | Complex documents |

## Fixed-Size with Overlap

```python
def fixed_chunk(text: str, chunk_size: int, overlap: int):
    chunks = []
    start = 0
    while start < len(text):
        end = start + chunk_size
        chunks.append(text[start:end])
        start += chunk_size - overlap
    return chunks
```

Overlap prevents cutting off meaning at boundaries. Typical overlap: 10-20% of chunk size.

## Recursive Character Splitter (LangChain)

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    separators=["\n\n", "\n", ".", " ", ""]
)
chunks = splitter.split_text(document)
```

## Semantic Chunking

Uses embedding similarity to detect topic shifts:

```python
sentences = sent_tokenize(text)
embeddings = embed_model.encode(sentences)
threshold = 0.3  # Cosine distance threshold

chunks = []
current_chunk = [sentences[0]]

for i in range(1, len(sentences)):
    similarity = cosine_similarity(embeddings[i-1], embeddings[i])
    if similarity < threshold:
        chunks.append(" ".join(current_chunk))
        current_chunk = []
    current_chunk.append(sentences[i])
```

## Guidelines

| Document Type | Recommended Chunk Size | Strategy |
|---------------|------------------------|----------|
| Code | 200-500 tokens | Recursive (function boundaries) |
| News articles | 500-1000 tokens | Recursive |
| Research papers | 300-600 tokens | Semantic (section-based) |
| Legal docs | 400-800 tokens | Document-based (clause-aware) |
| Books | 1000-2000 tokens | Semantic with summaries |

**Next**: [[Vector Databases for RAG]] — Store and search embeddings