---
id: a1b2c3d4-1016-4000-8000-000000000016
title: Monorepo vs Polyrepo
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001016
---
# Monorepo vs Polyrepo

Monorepo (single repository) and polyrepo (many repositories) are two approaches to organizing code. Each has tradeoffs.

## Comparison

| Aspect | Monorepo | Polyrepo |
|--------|----------|----------|
| Code sharing | Trivial (all in one repo) | Via packages/dependencies |
| Atomic commits | Cross-project changes in one commit | Multiple PRs across repos |
| CI setup | Complex (smart caching) | Simpler (per-repo) |
| Tooling requirements | High (specialized tools) | Standard git |
| Team autonomy | Lower (shared conventions) | Higher (own rules) |
| Repository size | Very large | Small |
| CI speed | Slower (more to test) | Faster (focused builds) |

## Monorepo Pros

- Atomic refactoring across projects
- Shared tooling and linting
- Easier code reuse (no package publish needed)
- Unified versioning
- Single CI pipeline for cross-cutting changes
- Simplified dependency management

## Monorepo Cons

- Requires specialized tools (Bazel, Nx, Turborepo)
- Larger clone and history
- CI can become slow without smart caching
- Needs strict ownership policies (CODEOWNERS)
- Can encourage tight coupling

## Polyrepo Pros

- Independent versioning and release cycles
- Teams own their tech stack decisions
- Simpler git history per repo
- Easier onboarding (smaller scope)
- Can use standard git, no special tools

## Polyrepo Cons

- Cross-project changes require multiple PRs
- Package publishing overhead
- Version drift between services
- Harder to enforce consistency
- Duplicated configuration boilerplate

## Tooling

| Tool | Type | Best For |
|------|------|----------|
| Bazel | Build system | Large monorepos (Google-scale) |
| Nx | Build orchestrator | JS/TS monorepos |
| Turborepo | Build cache & orchestration | JS/TS monorepos |
| Lerna | Multi-package JS | Publishing packages |
| Rush | Monorepo manager | Large TypeScript projects |
| Pants | Build system | Python monorepos |

## Decision Guide

Choose **monorepo** when:
- Tightly coupled projects with shared code
- You need atomic cross-cutting changes
- Your team is small to medium
- You can invest in tooling

Choose **polyrepo** when:
- Teams are independent and distributed
- Projects have different release cadences
- You want team ownership of tech choices
- You lack tooling investment capacity

**Links**: [[Git Submodules]] | [[Git Worktrees]] | [[Git Workflows]] | [[Code Review Best Practices]] | [[CI CD Pipelines]]
