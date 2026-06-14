---
id: a1b2c3d4-1043-4000-8000-000000000043
title: Multi-Modal RAG
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001043
---
# Multi-Modal RAG

Multi-modal RAG extends retrieval to multiple modalities — text, images, tables, audio, and video — enabling the LLM to reason across formats.

## Modalities

| Modality | Storage | Retrieval |
|----------|---------|-----------|
| Text | Vector index (text embeddings) | Semantic search |
| Images | Multi-modal embeddings (CLIP) + visual encoder | Image-to-image, text-to-image |
| Tables | Row/column embeddings + structured queries | Semantic + SQL hybrid |
| Audio | Audio embeddings (Whisper, CLAP) | Speech-to-text then search |
| Video | Keyframe extraction + audio transcript | Temporal + semantic search |

## Architecture

```
Document → Text Extractor → Text Chunks
         → Image Extractor → Images
         → Table Extractor → Tables
                    ↓
         Multi-Modal Embedding Model
                    ↓
         Unified Vector Index (with modality metadata)

Query → Multi-Modal Encoder → Search → Retrieved chunks → LLM
```

## Embedding Approaches

| Approach | Model | Space | Quality |
|----------|-------|-------|---------|
| Separate encoders | CLIP (ViT + text) | Shared vision-language | Good |
| Unified encoder | GPT-4V, Gemini | Joint latent space | Best |
| Late interaction | ColBERT multi-vector | Token-level | Good for reranking |

## Image Retrieval

```python
from PIL import Image
import torch

# CLIP-based retrieval
model = CLIPModel.from_pretrained("openai/clip-vit-base-patch32")
processor = CLIPProcessor.from_pretrained("openai/clip-vit-base-patch32")

# Encode all images
image_embeds = [model.get_image_features(processor(images=img, return_tensors="pt")) 
                for img in images]

# Encode text query
text_embed = model.get_text_features(processor(text=query, return_tensors="pt"))

# Similarity search
scores = cosine_similarity(text_embed, torch.stack(image_embeds))
```

## Table Retrieval

| Method | How |
|--------|-----|
| Serialize + embed | "Row: name=Alice, age=30" → text embedding |
| Structure-aware | Encode rows, columns, and relationships separately |
| Hybrid | Embed row descriptions + query with SQL-like conditions |

## Prompting with Multi-Modal Context

```python
# Pass images directly to multi-modal LLM
messages = [
    {"role": "user", "content": [
        {"type": "text", "text": "Describe this diagram:"},
        {"type": "image_url", "image_url": {"url": retrieved_image_url}}
    ]}
]
```

## Chunking Strategies

| Modality | Chunk Strategy |
|----------|----------------|
| Text + image | Keep image references within text chunks |
| Tables | Store as separate chunks with caption |
| Video | Keyframe at every scene change + transcript alignment |

## Retrieval Fusion

```
Query → Retrieve text (top-k) + Retrieve images (top-k) + Retrieve tables (top-k)
                  ↓
         Unified context ranked by relevance
                  ↓
              LLM reasoning
```

## Evaluation

| Metric | Measures |
|--------|----------|
| Modality recall | Did we retrieve the right modalities? |
| Cross-modal relevance | Is the image relevant to the text context? |
| Answer faithfulness | Does the answer reference retrieved content? |

**Links**: [[RAG Architecture]] | [[Advanced RAG Patterns]] | [[Embedding Models for RAG]] | [[Computer Vision]] | [[Transformer Architecture]]
