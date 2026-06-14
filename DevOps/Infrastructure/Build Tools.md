---
id: new-002-0000-0000-0000-000000000002
title: Build Tools
language: markdown
tags: [devops, infrastructure, build-tools]
selection: null
isPinned: false
timestamp: 1781600000002
---
# Build Tools

**Links**: [[Developer Workflow Automation]] | [[Dev Environment Setup]] | [[CI CD Pipelines]] | [[Package Management]] | [[Web Development Fundamentals]]

## What are Build Tools?

Build tools automate transforming source code into production-ready assets: bundling modules, transpiling code, minifying files, and more.

## Module Bundlers

| Tool | Language | Strengths |
|------|----------|-----------|
| **Webpack** | JS/TS | Mature ecosystem, code splitting, loaders |
| **Vite** | JS/TS | Fast dev server (ESM native), HMR |
| **esbuild** | JS/TS | Extremely fast (Go-based), simple config |
| **Rollup** | JS/TS | Tree-shaking, library bundling |
| **Parcel** | JS/TS | Zero config, fast |

## JavaScript Transpilers

```bash
# Babel: converts modern JS to compatible versions
npm install --save-dev @babel/core @babel/preset-env

# TypeScript: compiles TS to JS
npm install --save-dev typescript
```

## Vite Config Example

```javascript
// vite.config.js
import { defineConfig } from 'vite'

export default defineConfig({
  root: '.',
  build: {
    outDir: 'dist',
    minify: 'esbuild',
    rollupOptions: {
      input: 'src/main.js'
    }
  },
  server: {
    port: 3000,
    proxy: {
      '/api': 'http://localhost:8000'
    }
  }
})
```

## Package Scripts

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext .js,.ts",
    "test": "vitest run",
    "typecheck": "tsc --noEmit"
  }
}
```

**Next**: [[Cloud Computing]] — Cloud service models