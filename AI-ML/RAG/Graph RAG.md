---
id: a1b2c3d4-1042-4000-8000-000000000042
title: Graph RAG
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001042
---
# Graph RAG

Graph RAG enhances retrieval by leveraging knowledge graphs — capturing entities, relationships, and structural connections that dense retrieval alone misses.

## Why Graphs for RAG?

| Limitation of Vector RAG | Graph RAG Solution |
|--------------------------|-------------------|
| Semantic similarity ≠ factual relevance | Relationship paths capture factual connections |
| No entity resolution | Named entity recognition + linking |
| Missing multi-hop reasoning | Traverse edges across entities |
| Poor at aggregating information | Graph queries group by entity/relation |

## Graph RAG Pipeline

```
Documents → Entity Extraction → Relation Extraction → Knowledge Graph
                                                            ↓
Query → Entity Linking → Graph Traversal → Context → LLM
```

## Extraction

Extract entities and relations from documents:

```json
{
  "entities": [
    {"name": "GPT-4", "type": "Model", "mentions": ["GPT4", "GPT 4"]},
    {"name": "OpenAI", "type": "Organization"}
  ],
  "relations": [
    {"source": "OpenAI", "target": "GPT-4", "type": "developed_by"},
    {"source": "GPT-4", "target": "Transformer", "type": "based_on"}
  ]
}
```

Tools: LLM-based extraction, spaCy, GLiNER, Relik.

## Graph Traversal Strategies

| Strategy | Description | Example |
|----------|-------------|---------|
| BFS from seed | Find related entities 1-3 hops out | "What models did OpenAI develop?" |
| Shortest path | Connect query entities through graph | "How is GPT-4 related to BERT?" |
| PageRank/centrality | Find most important entities in subgraph | "What are the key concepts?" |
| Community detection | Cluster related entity groups | "Group papers by research area" |

## Indexing Approaches

| Approach | How | Best For |
|----------|-----|----------|
| Graph + Vector hybrid | Entities embedded + stored in vector DB + graph edges stored separately | Best recall, most complex |
| Graph as metadata | Entities as metadata filter on vector search | Simple, limited traversal |
| Text-attributed graphs | Node text embedded, graph structure indexed | Rich representation |
| Microsoft GraphRAG | Community summarization + hierarchical clustering | Large-scale document sets |

## Microsoft GraphRAG

A published approach that:

1. Extracts entities/relations into a graph
2. Clusters entities into communities (Leiden algorithm)
3. Generates summaries per community
4. Answers queries by searching community summaries

```
Documents → Entity Graph → Community Detection → Summaries → Q&A
```

- **Global search**: Answer broad questions (community summaries)
- **Local search**: Answer specific questions (entity neighborhood)

## Query Decomposition with Graphs

```
Query: "What drugs interact with aspirin for heart patients?"

1. Extract entities: aspirin, heart disease
2. Traverse graph: aspirin → interacts_with → [drugs]
                              ↓
                   used_for → heart_disease → treated_by → [drugs]
3. Intersect: [drugs that interact with aspirin AND treat heart disease]
```

## Implementation Options

| Option | Setup | Scale |
|--------|-------|-------|
| NetworkX + LLM | Python, single machine | Small graphs |
| Neo4j + LLM | Graph DB, Cypher queries | Medium |
| NebulaGraph | Distributed graph DB | Large |
| Microsoft GraphRAG | Full pipeline (extract to answer) | Large |

## Tradeoffs

| Pro | Con |
|-----|-----|
| Higher factual accuracy | Slower extraction pipeline |
| Multi-hop reasoning | Entity resolution errors |
| Aggregation across documents | More complex infrastructure |
| Traceable evidence paths | Graph maintenance overhead |

**Links**: [[RAG Architecture]] | [[Advanced RAG Patterns]] | [[Retrieval Strategies]] | [[Chunking Strategies]] | [[NLP Pipeline Design]]
