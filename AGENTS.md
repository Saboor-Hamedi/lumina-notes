---
id: AGENTS
title: AGENTS
language: markdown
tags: ''
selection: null
isPinned: false
customIcon: null
timestamp: 1781947503270.9707
---
# AGENTS.md — Lumina Knowledge Vault

## What this is
An **Obsidian vault** + custom **Lumina** viewer — a knowledge base of ~400 interconnected markdown notes (~190K lines) across AI/ML, System Design, Databases, DevOps, Security, Web Dev, Git, Testing, and Software Engineering. This is NOT a code project — no build/test/lint/typecheck commands exist.

## Entry points
- `_MOC.md` — master index with full mindmap
- `INDEX.md` — domain map linking every MOC
- `LEARNING_ROADMAP.md` — prioritized reading plan
- Every subfolder has a `_MOC.md` as its index

## Wiki-link convention
Always use full path from vault root:
- `[[Folder/Subfolder/Note Name]]` — correct
- `[[Note Name]]` — will NOT resolve correctly
- `[[Folder/_MOC]]` — correct for MOC links

## Note structure
Every note has YAML frontmatter: `id`, `title`, `language: markdown`, `tags`, `timestamp`, `isPinned`. Notes use Mermaid diagrams (graph, sequence, mindmap), code examples, and comparison tables extensively.

## Folder naming convention
- **PascalCase** for knowledge domains (`AI-ML/`, `DevOps/`, `System-Design/`, `Security/`, `Testing/`, `Web-Dev/`, `Software-Engineering/`, `Data-Science/`)
- **lowercase** for teaching workspaces (`database/`, `docker/`, `linux/`, `networking/`, `python/`, `Git/`)
- **ALL-CAPS** for root meta files (`AGENTS.md`, `INDEX.md`, `LEARNING_ROADMAP.md`)
- Every subfolder must have a `_MOC.md` as its index

## Teaching workspaces
Folders `database/`, `linux/`, `docker/`, `networking/`, `python/` follow:
`MISSION.md` → `NOTES.md` → `RESOURCES.md` + `lessons/` + `learning-records/`

## Agent gotchas
- `aiexclusive.md` — AI exclusion patterns (like .gitignore for AI)
- `.lumina/settings.json` contains a DeepSeek API key — never commit or expose
- `_scratch/` — unrelated project docs and sandbox files, ignore
- `New Note.md` — bare frontmatter template for creating notes
- `.agents/skills/` — 10 loadable agent skills (teach, qa, triage, etc.)
- `skills-lock.json` — installed skill versions
- Obsidian plugins: icon-folder, file-color, ink, persistent-graph
