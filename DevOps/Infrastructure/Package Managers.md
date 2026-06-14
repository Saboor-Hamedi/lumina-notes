---
id: a1b2c3d4-1014-4000-8000-000000000014
title: Package Managers
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001014
---

**Links**: [[Build Tools]] | [[Dev Environment Setup]] | [[Shell Scripting]] | [[Developer Workflow Automation]] | [[Vagrant]] | [[Ansible]]


# Package Managers

Package managers automate installing, updating, configuring, and removing software dependencies. Each ecosystem has its own.

## Language Package Managers

| Language | Manager | Registry | Lock File |
|----------|---------|----------|-----------|
| JavaScript | npm/yarn/pnpm | npmjs.com | package-lock.json |
| Python | pip/poetry | PyPI | requirements.txt / poetry.lock |
| Rust | cargo | crates.io | Cargo.lock |
| Go | go mod | proxy.golang.org | go.sum |
| Java | Maven/Gradle | Maven Central | pom.xml / build.gradle |
| Ruby | bundler | rubygems.org | Gemfile.lock |
| .NET | NuGet | nuget.org | packages.lock.json |
| Swift | SPM | swift.org | Package.resolved |

## Key Features

| Feature | npm | pip | cargo | go mod |
|---------|-----|-----|-------|--------|
| Lock file | ✓ | ✗ (pip) / ✓ (poetry) | ✓ | ✓ |
| Reproducible builds | ✓ | With lock | ✓ | ✓ |
| Private registries | ✓ | ✓ | ✓ | Via replace |
| Semantic versioning | ✓ | ✓ | ✓ | ✓ |
| Workspaces/Monorepo | ✓ | ✗ | ✓ | ✓ |
| Pre/post scripts | ✓ | ✗ | ✓ | ✗ |

## SemVer (Semantic Versioning)

```
MAJOR.MINOR.PATCH

MAJOR: Breaking changes
MINOR: New features (backward compatible)
PATCH: Bug fixes (backward compatible)
```

**Prefixes**: `^` (compatible), `~` (approximately), no prefix (exact)

## Lock Files

Lock files pin exact versions of every transitive dependency. Benefits:
- Reproducible builds across environments
- Protection against supply chain attacks (accidental upgrades)
- Faster installs (skip resolution step)

## Version Resolution Strategies

| Strategy | Behavior | Example |
|----------|----------|---------|
| Hoisted | Single version at top of tree | npm (default) |
| Isolated | Each package gets its own version | npm --legacy-bundling |
| Exact | Use exactly what's specified | cargo, go mod |

## Best Practices

- Commit lock files to version control
- Use `npm audit`, `pip audit`, `cargo audit` for vulnerabilities
- Pin exact versions in production
- Use `dependabot` or `renovate` for automated updates
- Prefer `pnpm` over `npm` for disk efficiency (hard links)
- Use virtual environments (venv, conda) for Python isolation

**Links**: [[Dev Environment Setup]] | [[CI CD Pipelines]] | [[Docker Containers]] | [[Python Virtual Environments]] | [[Build Tools]]
