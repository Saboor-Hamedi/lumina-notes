<p align="center">

```mermaid
mindmap
  root((lumina-notes))
    AI-ML
      Deep-Learning
        Machine-Learning
      NLP
        LLMs
        Tokenization
        Prompt Engineering
        Alignment
      RAG
        Retrieval
        Embeddings
        Reranking
        Evaluation
    Data-Science
      R
      Pandas
      scikit-learn
      Data Engineering
    DevOps
      CI-CD
      Containers
      Infrastructure
      Monitoring
    Git
      Basics
      Branching
      Internals
      Hooks
    Security
      OAuth
      TLS
      OWASP
      Zero Trust
    Software-Engineering
      Design Patterns
      Clean Code
      Code Review
      Agile
      Testing
    System-Design
      Architecture
      Databases 55
      Algorithms
      Data Structures
    Web-Dev
      Frontend
      APIs
      Protocols
      Languages 34
```

</p>

<div align="center">

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat&logo=typescript&logoColor=white)
![Rust](https://img.shields.io/badge/Rust-000000?style=flat&logo=rust&logoColor=white)
![Go](https://img.shields.io/badge/Go-00ADD8?style=flat&logo=go&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-CC2927?style=flat&logo=postgresql&logoColor=white)
![Markdown](https://img.shields.io/badge/Markdown-000000?style=flat&logo=markdown&logoColor=white)
![Mermaid](https://img.shields.io/badge/Mermaid-FF3670?style=flat&logo=mermaid&logoColor=white)
![Obsidian](https://img.shields.io/badge/Obsidian-483699?style=flat&logo=obsidian&logoColor=white)

</div>

<p align="center">
  A comprehensive engineering knowledge base -- 400+ interconnected notes across AI/ML, system design, databases, DevOps, security, and software engineering. Built as an Obsidian vault with Mermaid diagrams, deep cross-linking, and hierarchical Maps of Content.
</p>

<p align="center">
  <a href="#structure">Structure</a> .
  <a href="#domains">Domains</a> .
  <a href="#features">Features</a> .
  <a href="#usage">Usage</a> .
  <a href="#stats">Stats</a> .
  <a href="#navigate">Navigate</a>
</p>

---

## Overview

lumina-notes is a structured second brain for engineers. Every folder contains a `_MOC.md` (Map of Content) index that links to all notes within it. Notes are deeply interconnected through `[[wiki-links]]`, forming a knowledge graph that mirrors real-world engineering relationships.

The vault prioritizes depth over breadth -- each note includes code examples, configuration snippets, architecture diagrams, and decision matrices. Mermaid diagrams are used throughout for visual explanation of complex systems.

---

## Structure

```
lumina-notes/
  _MOC.md                        Root master index
  AI-ML/                         Artificial Intelligence & Machine Learning
    Deep-Learning/               Transformers, attention, training, inference
      Machine-Learning/          Classical ML: ensembles, clustering, MLOps
    NLP/                         Tokenization, prompting, LLMs, alignment
    RAG/                         Retrieval-Augmented Generation full stack
  Data-Science/                  Data wrangling, analysis, engineering
  DevOps/                        CI/CD, containers, infrastructure, monitoring
  Git/                           45 notes from basics to internals
  Security/                      OAuth, TLS, OWASP, zero trust, cryptography
  Software-Engineering/          Design patterns, code review, testing, agile
  System-Design/                 Architecture, databases, algorithms, data structures
    Databases/                   55 notes on SQL, NoSQL, engines, indexing, sharding
  Testing/                       Unit, integration, performance, property-based
  Web-Dev/                       Frontend, protocols, APIs, programming languages
    Programming/                 34 language and framework deep dives
```

---

## Domains

| Domain | Notes | Key Topics |
|--------|-------|------------|
| AI/ML and NLP | 80+ | LLMs, transformers, RAG, prompt engineering, alignment, RLHF |
| Databases | 55 | Indexing, sharding, transactions, replication, engines, modeling |
| System Design | 85+ | Architecture patterns, microservices, CAP theorem, DDD, consensus |
| DevOps | 40+ | Docker, Kubernetes, CI/CD, monitoring, observability, IaC |
| Git | 45 | Complete reference from init to internals, workflows, hooks |
| Security | 15 | OAuth 2.0, TLS 1.3, OWASP Top 10, zero trust, threat modeling |
| Software Engineering | 30+ | SOLID, design patterns, code review, testing, agile practices |
| Data Science | 5+ | R, pandas, scikit-learn, data engineering pipelines |
| Web Development | 30+ | React, HTTP, GraphQL, WebSocket, WASM, PWAs |
| Programming Languages | 34 | Python, Rust, Go, TypeScript, Java, Haskell, Julia, and more |

---

## Features

### Maps of Content (MOCs)

Every folder contains a `_MOC.md` file that serves as an index, featuring:
- A Mermaid graph showing topic relationships
- A table of contents linking to all notes
- Cross-domain links to related topics in other folders

### Mermaid Diagrams

Notes extensively use Mermaid for visual explanation:
- Architecture flowcharts (system design, data pipelines)
- Sequence diagrams (protocols, request flows)
- Gantt charts (tool evolution, technology timelines)
- Entity relationship diagrams (database schemas)
- Graphs and trees (algorithm visualization)
- Quadrant charts (tradeoff analysis)

### Cross-Linking

Notes are connected through bidirectional `[[wiki-links]]`:
- Direct prerequisite or reference relationships
- Lateral connections to adjacent topics
- Aggregated cross-domain connections in MOC files

### Structural Consistency

Each note follows a predictable format:
- YAML frontmatter (id, title, tags, timestamp)
- Overview section defining the concept
- Deep dive with code examples, tables, and diagrams
- Practical guidance (best practices, pitfalls, decision trees)
- Links to related notes and further reading

---

## Usage

### As an Obsidian Vault (Recommended)

```bash
git clone https://github.com/Saboor-Hamedi/lumina-notes.git
```

Open Obsidian, select "Open folder as vault", and choose the cloned directory. Enable community plugins when prompted -- the vault includes pre-configured settings, themes (Minimal, Obsidianite, Wasp), and plugins (calendar, file colors, icon folder, style settings).

### As a Static Reference

Browse the Markdown files directly on GitHub. Mermaid diagrams render natively. Use the `_MOC.md` files as entry points for each domain.

### For Self-Hosted Knowledge Base

The Markdown files can be ingested by any static site generator (MkDocs, Docusaurus, Quartz) for a searchable web-based knowledge base.

---

## Stats

| Metric | Value |
|--------|-------|
| Total notes | 400+ |
| Total lines | ~190,000 |
| Programming languages covered | 34 |
| Database notes | 55 |
| Git notes | 45 |
| MOC index files | 21 |
| Root MOC cross-links | ~100 |
| Domains | 10 |

---

## Navigate

### Learning AI/ML
`AI-ML/_MOC.md` to `AI-ML/NLP/_MOC.md` for LLMs to `AI-ML/RAG/_MOC.md` for retrieval systems.

### Preparing for system design interviews
`System-Design/_MOC.md` to `System-Design/Architecture/_MOC.md` for patterns to `System-Design/Databases/_MOC.md` for storage.

### Setting up DevOps
`DevOps/_MOC.md` to `DevOps/Containers/_MOC.md` for containerization to `DevOps/CI-CD/_MOC.md` for pipelines.

### Broad overview
Open `_MOC.md` at the root -- the master mind map of all topics and their relationships.

---

<p align="center">
  <sub>MIT License. Built by Saboor Hamedi.</sub>
</p>
