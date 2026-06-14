# lumina-notes

A comprehensive engineering knowledge base covering software engineering, system design, AI/ML, DevOps, security, and data science. Built as an Obsidian vault with deep cross-linking, Mermaid diagrams, and hierarchical Maps of Content.

---

## Overview

lumina-notes is a structured second brain for engineers. It contains 400+ notes across 10 domains, organized with a Map of Content (MOC) pattern where every folder has a `_MOC.md` index that links to all notes within it. Notes are deeply interconnected through `[[wiki-links]]`, forming a graph that mirrors real-world engineering relationships.

The vault prioritizes depth over breadth -- each note includes code examples, configuration snippets, architecture diagrams, and decision matrices. Mermaid diagrams are used throughout for visual explanation of complex systems.

---

## Structure

```
lumina-notes/
├── _MOC.md                          # Root master index
├── AI-ML/                           # Artificial Intelligence & Machine Learning
│   ├── Deep-Learning/               # Transformers, attention, training, inference
│   │   └── Machine-Learning/        # Classical ML: ensembles, clustering, MLOps
│   ├── NLP/                         # Tokenization, prompting, LLMs, alignment
│   └── RAG/                         # Retrieval-Augmented Generation full stack
├── Data-Science/                    # Data wrangling, analysis, engineering
├── DevOps/                          # CI/CD, containers, infrastructure, monitoring
├── Git/                             # 45 notes covering git from basics to internals
├── Security/                        # OAuth, TLS, OWASP, zero trust, cryptography
├── Software-Engineering/            # Design patterns, code review, testing, agile
├── System-Design/                   # Architecture, databases, algorithms, data structures
│   └── Databases/                   # 55 notes on SQL, NoSQL, engines, indexing, sharding
├── Testing/                         # Unit, integration, performance, property-based
└── Web-Dev/                         # Frontend, protocols, APIs, programming languages
    └── Programming/                 # 34 language/framework deep dives
```

---

## Domain Breakdown

| Domain | Notes | Key Topics |
|--------|-------|------------|
| AI/ML & NLP | 80+ | LLMs, transformers, RAG, prompt engineering, alignment, RLHF |
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
- Graphs and trees (data structures, algorithm visualization)
- Quadrant charts (tradeoff analysis)

### Cross-Linking

Notes are connected through bidirectional `[[wiki-links]]` following patterns:
- `Links` header: direct prerequisite or reference relationships
- `See also` header: lateral connections to adjacent topics
- MOC files: aggregated cross-domain connections

### Structural Consistency

Each note follows a predictable format:
- YAML frontmatter (id, title, tags, timestamp)
- Overview section defining the concept
- Deep dive with code examples, tables, diagrams
- Practical guidance (best practices, pitfalls, decision trees)
- Links to related notes

---

## How to Use

### As an Obsidian Vault (Recommended)

1. Clone the repository:
   ```bash
   git clone https://github.com/Saboor-Hamedi/lumina-notes.git
   ```
2. Open Obsidian
3. Select "Open folder as vault" and choose the cloned directory
4. Enable community plugins if prompted (the vault uses several)

The vault includes pre-configured Obsidian settings, themes (Minimal, Obsidianite, Wasp), and plugins (calendar, file colors, icon folder, style settings).

### As a Static Reference

Browse the Markdown files directly on GitHub or any Markdown viewer. Mermaid diagrams render natively on GitHub. Navigate using the `_MOC.md` files as entry points.

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

## Navigation Guide

### If you are learning AI/ML
Start at `AI-ML/_MOC.md` -> `AI-ML/NLP/_MOC.md` for LLMs -> `AI-ML/RAG/_MOC.md` for retrieval systems.

### If you are preparing for system design interviews
Start at `System-Design/_MOC.md` -> `System-Design/Architecture/_MOC.md` for patterns -> `System-Design/Databases/_MOC.md` for storage.

### If you are setting up DevOps
Start at `DevOps/_MOC.md` -> `DevOps/Containers/_MOC.md` for containerization -> `DevOps/CI-CD/_MOC.md` for pipelines.

### If you want a broad overview
Open `_MOC.md` at the root, which contains the master mind map of all topics and their relationships.

---

## Contributing

This is a personal knowledge base, but suggestions and corrections are welcome via issues or pull requests. If you find an error or want to suggest an addition, please open an issue with the relevant context.

---

## License

MIT

---

## Author

Built by Saboor Hamedi.
