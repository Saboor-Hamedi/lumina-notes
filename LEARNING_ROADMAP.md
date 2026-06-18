---
id: LEARNING_ROADMAP
title: LEARNING_ROADMAP
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781766713593
---
# Learning Roadmap

A prioritized reading plan for your vault. Designed for senior-level engineers with limited time — skip what you know, dive where you're curious.

**How to use**: Each track lists notes in dependency order. Time estimates assume focused reading of the intro + first `##` heading + scanning the rest. Double time for deep study (code, diagrams, examples).


@lumina -> #lumina

---

## Priority Tracks

### Track 1: NLP / LLM

Your primary focus. Covers tokenization through production deployment.

| # | Note | Time | Why this order |
| --- | --- | --- | --- |
| 1 | [[AI-ML/NLP/Tokenization]] | 8 min | Foundation — every model starts here |
| 2 | [[AI-ML/NLP/LLM/_MOC]] | 3 min | Survey the LLM landscape, pick your entry |
| 3 | [[AI-ML/NLP/LLM/01 Architecture Overview]] | 15 min | Transformer architecture, GPT vs LLaMA vs Mistral |
| 4 | [[AI-ML/NLP/LLM/02 Tokenization & Generation]] | 12 min | BPE, decoding strategies, sampling |
| 5 | [[AI-ML/NLP/Text Embedding Models]] | 10 min | Embeddings bridge NLP and RAG |
| 6 | [[AI-ML/NLP/LLM/05 Prompting Strategies]] | 12 min | Prompt engineering fundamentals |
| 7 | [[AI-ML/NLP/Prompt Engineering]] | 15 min | Structured prompting, Chain-of-Thought |
| 8 | [[AI-ML/NLP/Advanced Prompting Techniques/_MOC]] | 3 min | Survey 9 sub-notes, pick 2-3 |
| 9 | [[AI-ML/NLP/LLM/04 Fine-Tuning]] | 12 min | SFT, LoRA, QLoRA, when to fine-tune vs prompt |
| 10 | [[AI-ML/NLP/LLM Alignment]] | 10 min | RLHF, DPO, preference optimization |
| 11 | [[AI-ML/NLP/LLM/06 Tool Use & Multi-Agent]] | 10 min | Function calling, tool-use patterns |
| 12 | [[AI-ML/NLP/LLM Agents Framework]] | 12 min | Agent architectures, planning, memory |
| 13 | [[AI-ML/NLP/LLM/07 RAG & Inference Optimization]] | 10 min | RAG patterns, chunking, hybrid search |
| 14 | [[AI-ML/RAG/_MOC ]] | 3 min | Survey RAG folder — retrieval, reranking, eval |
| 15 | [[AI-ML/NLP/Inference Optimization]] | 10 min | KV cache, speculative decoding, Flash Attention |
| 16 | [[AI-ML/NLP/LLM Safety and Guardrails ]] | 10 min | Safety, alignment tax, red-teaming |
|  |  |  |  |

**Prerequisite branches**: 
- NLP Pipeline → RAG: steps 5, 13, 14
- NLP → Production: steps 11, 15, 16

---

### Track 2: Pandas

Data manipulation with Python. Sequential — each builds on the last.

| # | Note | Time | Why |
| --- | --- | --- | --- |
| 1 | [[Data-Science/Pandas/01 Basics]] | 10 min | Series, DataFrame, dtypes, memory |
| 2 | [[Data-Science/Pandas/02 I-O]] | 8 min | CSV, SQL, Parquet, Feather, JSON |
| 3 | [[Data-Science/Pandas/03 Cleaning]] | 10 min | Missing data, duplicates, type conversion |
| 4 | [[Data-Science/Pandas/04 Selection Indexing]] | 12 min | loc/iloc, boolean, MultiIndex |
| 5 | [[Data-Science/Pandas/05 GroupBy Aggregation]] | 12 min | groupby, agg, pivot_table, crosstab |
| 6 | [[Data-Science/Pandas/06 Merge Join Concat]] | 10 min | All join types, concat, update |
| 7 | [[Data-Science/Pandas/07 Transform String]] | 8 min | apply, map, .str accessor, categoricals |
| 8 | [[Data-Science/Pandas/08 Time Series]] | 12 min | date_range, resample, rolling, ewm |
| 9 | [[Data-Science/Pandas/09 Visualization]] | 8 min | .plot API, matplotlib/seaborn |
| 10 | [[Data-Science/Pandas/10 Advanced]] | 15 min | Memory optimization, chunks, MultiIndex deep, Polars comparison |
| 11 | [[Data-Science/Pandas/11 End-to-End Pipeline]] | 10 min | Full pipeline: ingest → clean → transform → viz |


**Shortcut**: If you already use Pandas daily, start at 05 and 08 (most practical leverage). Read 10 and 11 for optimization and architecture.

---

### Track 3: PostgreSQL

From features to production tuning.

| # | Note | Time | Why |
|---|------|------|-----|
| 1 | [[System-Design/Databases/PostgreSQL Features]] | 12 min | MVCC, WAL, TOAST, vacuum, extensions ecosystem |
| 2 | [[System-Design/Databases/PostgreSQL Performance Tuning]] | 20 min | Memory, I/O, query planning, connection pooling, vacuum tuning |
| 3 | [[System-Design/Databases/PostgreSQL Extensions]] | 8 min | PostGIS, pgvector, TimescaleDB, Citus, pg_partman |
| 4 | [[System-Design/Databases/Database Indexing Deep Dive]] | 15 min | B-tree, GiST, GIN, BRIN, multi-column, partial |
| 5 | [[System-Design/Databases/SQL Query Optimization]] | 12 min | EXPLAIN ANALYZE, scan types, join strategies |
| 6 | [[System-Design/Databases/Database Engines Compared]] | 10 min | PostgreSQL vs MySQL vs SQLite — architecture tradeoffs |
| 7 | [[System-Design/Databases/Database Transaction Isolation Levels]] | 10 min | MVCC details, SSI, snapshot isolation |
| 8 | [[System-Design/Databases/Database Sharding]] | 12 min | Horizontal scaling, Citus, partitioning |
| 9 | [[System-Design/Databases/Database Replication Strategies]] | 10 min | Streaming replication, logical replication, failover |

**Dependencies**: Indexing (4) helps understand Performance Tuning (2). Isolation Levels (7) builds on Features (1). Sharding + Replication (8, 9) are standalone advanced topics.

---

## Secondary Tracks

### Track 4: System Design (Architecture)

Start here if you want the big picture before database internals.

| # | Note | Time | Why |
|---|------|------|-----|
| 1 | [[System-Design/Architecture/System Design Fundamentals]] | 15 min | Scalability, availability, latency, CAP |
| 2 | [[System-Design/Architecture/Microservices Architecture]] | 12 min | Decomposition, communication, data ownership |
| 3 | [[System-Design/Architecture/Event-Driven Architecture]] | 12 min | Events, streams, CQRS, saga patterns |
| 4 | [[System-Design/Architecture/CAP Theorem and PACELC]] | 8 min | Consistency vs availability tradeoffs |
| 5 | [[System-Design/Databases/Caching Strategies]] | 10 min | Cache-aside, write-through, CDN, Redis patterns |
| 6 | [[System-Design/Databases/Message Queues]] | 10 min | Kafka vs RabbitMQ vs Pulsar, patterns |
| 7 | [[System-Design/Databases/Apache Kafka Deep Dive]] | 15 min | Partitions, replication, producer/consumer design |

**When to dive**: If you're preparing for system design interviews, start with 1 → 2 → 4 → 5. For day-to-day architecture decisions, 3 → 6 → 7.

---

### Track 5: LLM Deep Dive

If you want the full LLM sub-folder (10 notes, already organized sequentially):

| # | Note | Time | Topic |
|---|------|------|-------|
| 1 | [[AI-ML/NLP/LLM/01 Architecture Overview]] | 15 min | Transformer variants, scaling laws |
| 2 | [[AI-ML/NLP/LLM/02 Tokenization & Generation]] | 12 min | Tokenizers, decoding, sampling |
| 3 | [[AI-ML/NLP/LLM/03 Training & Data]] | 12 min | Pre-training, data mixing, curriculum |
| 4 | [[AI-ML/NLP/LLM/04 Fine-Tuning]] | 12 min | SFT, LoRA/QLoRA, RLHF, DPO |
| 5 | [[AI-ML/NLP/LLM/05 Prompting Strategies]] | 12 min | System prompts, few-shot, COT |
| 6 | [[AI-ML/NLP/LLM/06 Tool Use & Multi-Agent]] | 10 min | Function calling, agent orchestration |
| 7 | [[AI-ML/NLP/LLM/07 RAG & Inference Optimization]] | 10 min | RAG patterns, speculative decoding |
| 8 | [[AI-ML/NLP/LLM/08 Safety, Evaluation & Hallucination]] | 10 min | Benchmarks, eval suites, guardrails |
| 9 | [[AI-ML/NLP/LLM/09 Models, Trends & Selection]] | 8 min | Model comparison, selection criteria |
| 10 | [[AI-ML/NLP/LLM/10 Glossary]] | 5 min | Terms reference |

**Read linearly** — each note builds on the previous. Total: ~100 min. Spread across 5-10 sessions.

---

## Session Templates

### 5-Minute Burst
- Open any `_MOC.md` and scan the table. Read the descriptions. Close.
- **Goal**: Build mental map of what exists for future reference.

### 15-Minute Session
1. Pick one note from a priority track (2 min scanning the MOC)
2. Read intro paragraph + first `##` section (8 min)
3. Active recall: cover the page, explain the concept aloud (5 min)

### 30-Minute Session
1. Pick 2 related notes from the same track (3 min)
2. Read both intros + first two `##` sections each (15 min)
3. Compare and contrast: what's the relationship? (7 min)
4. Write one `**Key takeaway**:` line at the top of each note in your own words (5 min)

---

## Cross-Domain Connections

```
NLP Embeddings ──────► RAG (Embedding Models → Chunking → Retrieval)
PostgreSQL Tuning ───► System Design (Sharding → Replication → CAP)
Pandas Pipelines ────► Data Engineering (ETL → Orchestration → Warehousing)
LLM Agents ──────────► Security (API Security → Guardrails → Vault Secrets)
Prompt Engineering ──► Testing (Evaluation → Benchmarks → Red-Teaming)
```

---

## Quick-Start: This Week

If you have 45 minutes total this week:

| Day | Time | What |
|-----|------|------|
| Mon | 15 min | [[AI-ML/NLP/Tokenization]] — the NLP starting point |
| Wed | 15 min | [[Data-Science/Pandas/05 GroupBy Aggregation]] — highest-leverage Pandas topic |
| Fri | 15 min | [[System-Design/Databases/PostgreSQL Features]] — database foundation |

Use `LEARNING_ROADMAP.md` at vault root for the full roadmap and links to every track.
