---
id: a1b2c3d4-1171-4000-8000-000000000171
title: Monorepo with Nx and Turborepo
language: markdown
tags: [software-engineering]
selection: null
isPinned: false
timestamp: 1781500001171
---
# Monorepo with Nx and Turborepo

Monorepos store multiple projects in a single repository. Nx and Turborepo provide tools for dependency management, caching, parallel execution, and incrementality.

## Why Monorepo

| Pro | Con |
|-----|-----|
| Shared code and types | Larger clone and git history |
| Atomic commits across projects | CI complexity |
| Unified tooling and config | Permission management |
| Easier refactoring | Learning curve |
| Cross-project visibility | Slow `git status` (partial fix: sparse checkout) |

## Nx

Nx is a full-featured build system with computation caching, dependency graph visualization, code generation, and incremental builds.

```bash
# Create Nx workspace
npx create-nx-workspace@latest my-workspace --preset=ts

# Add applications
npx nx g @nx/react:app web
npx nx g @nx/express:app api
npx nx g @nx/node:app worker

# Add libraries
npx nx g @nx/react:lib shared-ui
npx nx g @nx/js:lib shared-types
```

### Nx Project Graph

```bash
# Visualize dependencies
npx nx graph

# Shows:
# web ──► shared-ui ──► shared-types
# api ──► shared-types
# worker ──► shared-types
```

### Nx Commands

```bash
# Run single project
npx nx build web

# Run affected by changes
npx nx affected:build
npx nx affected:test

# Run in parallel
npx nx run-many --target=build --all --parallel=5

# Run with dependency awareness
npx nx run-many --target=test --projects=web,api --with-deps

# Dry run (see what would run)
npx nx affected:build --dry-run
```

### Computation Caching

```json
// nx.json
{
    "tasksRunnerOptions": {
        "default": {
            "runner": "nx-cloud",     // Remote caching
            "options": {
                "cacheableOperations": ["build", "test", "lint", "e2e"],
                "accessToken": "SECRET",
                "canTrackAnalytics": false
            }
        }
    },
    "targetDefaults": {
        "build": {
            "dependsOn": ["^build"],  // Build dependencies first
            "inputs": ["!{projectRoot}/test/**/*"]  // Exclude tests from build cache
        },
        "test": {
            "inputs": ["default", "^default"]
        }
    }
}
```

## Turborepo

Turborepo is a faster, simpler alternative focused on caching and parallel execution.

```bash
# Create
npx create-turbo@latest

# Structure
my-turbo/
├── apps/
│   ├── web/         (Next.js)
│   └── api/         (Express)
├── packages/
│   ├── ui/          (shared React components)
│   ├── types/       (shared TypeScript types)
│   └── eslint-config/
├── turbo.json
└── package.json
```

```json
// turbo.json
{
    "$schema": "https://turbo.build/schema.json",
    "globalDependencies": ["**/.env.*local"],
    "pipeline": {
        "build": {
            "dependsOn": ["^build"],       // Build dependencies first
            "outputs": [".next/**", "dist/**"],
            "inputs": ["src/**", "tsconfig.json"]
        },
        "test": {
            "dependsOn": ["build"],
            "inputs": ["src/**/*.test.*", "src/**/*.spec.*"]
        },
        "lint": {},
        "dev": {
            "cache": false,               // Never cache dev
            "persistent": true             // Long-running process
        },
        "deploy": {
            "dependsOn": ["build", "test", "lint"]
        }
    }
}
```

```bash
# Run all builds
turbo run build

# Filter specific projects
turbo run build --filter=web
turbo run build --filter=./apps/*      # Glob filter
turbo run build --filter={packages/*}  # Package-based filter

# Run affected by changes
turbo run build --filter=[main]        # Changed since main

# Remote caching (Vercel)
turbo run build --token=TOKEN --team=TEAM
```

## Nx vs Turborepo

| Feature | Nx | Turborepo |
|---------|----|-----------|
| Language | TypeScript | TypeScript (Rust core coming) |
| Code generation | Excellent (generators) | None |
| Plugin ecosystem | Large | Small |
| Project graph | Built-in visualization | Terminal output |
| Affected commands | `nx affected:test` | `turbo run test --filter=[main]` |
| Remote caching | Nx Cloud | Vercel Remote Caching |
| Task orchestration | Yes (dependency aware) | Yes |
| Performance | Fast | Slightly faster (Rust) |
| Learning curve | Steeper | Gentler |

## Monorepo Structure

```
my-monorepo/
├── apps/
│   ├── web/                    # Next.js app
│   │   ├── src/
│   │   ├── package.json
│   │   └── tsconfig.json
│   └── api/                    # Express server
│       ├── src/
│       ├── package.json
│       └── tsconfig.json
├── packages/
│   ├── ui/                     # Shared UI components
│   ├── types/                  # Shared TypeScript types
│   ├── utils/                  # Shared utilities
│   ├── eslint-config/          # Shared ESLint config
│   └── tsconfig/               # Shared TypeScript config
├── tools/
│   ├── generators/             # Custom generators
│   └── scripts/                # Build scripts
├── .github/workflows/          # CI/CD
├── nx.json / turbo.json
├── package.json                # Root scripts
├── tsconfig.base.json          # Base TS config
└── .gitignore
```

## Package Manager Support

```bash
# npm workspaces
{
    "workspaces": ["apps/*", "packages/*"]
}

# Yarn workspaces
{
    "workspaces": ["apps/*", "packages/*"]
}

# pnpm workspaces (pnpm-workspace.yaml)
packages:
  - "apps/*"
  - "packages/*"
```

## Shared TypeScript Config

```json
// tsconfig.base.json
{
    "compilerOptions": {
        "target": "ES2022",
        "module": "ESNext",
        "moduleResolution": "bundler",
        "strict": true,
        "declaration": true,
        "declarationMap": true,
        "sourceMap": true,
        "composite": true,           // Required for project references
        "paths": {
            "@myorg/ui": ["./packages/ui/src"],
            "@myorg/types": ["./packages/types/src"],
            "@myorg/utils": ["./packages/utils/src"]
        }
    }
}
```

## CI Optimization

```yaml
# Nx affected — only build/test changed projects
jobs:
  build:
    steps:
      - run: npx nx affected:build --base=origin/main

# Turborepo filter — only build affected
jobs:
  build:
    steps:
      - run: turbo run build test --filter=[origin/main]
```
