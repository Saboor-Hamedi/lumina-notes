---
id: a1b2c3d4-1029-4000-8000-000000000029
title: React Setup and Tooling
language: markdown
tags: [web-dev, react, tooling, setup]
selection: null
isPinned: false
customIcon: null
timestamp: 1782000600004
---

# React Setup and Tooling

**Links**: [[React]] | [[Vite and esbuild]] | [[Next.js and Modern Web Frameworks]] | [[TypeScript]] | [[Build Tools]]

## Vite (Recommended)

Fastest setup with instant HMR:

```bash
npm create vite@latest my-app -- --template react
cd my-app
npm install
npm run dev
```

For TypeScript:

```bash
npm create vite@latest my-app -- --template react-ts
```

## Create React App (Legacy)

```bash
npx create-react-app my-app
cd my-app
npm start
```

Note: CRA is in maintenance mode. Prefer Vite or Next.js for new projects.

## Next.js (Full-Stack React)

```bash
npx create-next-app@latest my-app
cd my-app
npm run dev
```

## Project Skeleton

```
my-app/
├── public/
│   └── index.html
├── src/
│   ├── components/     # Reusable components
│   ├── pages/          # Page components
│   ├── hooks/          # Custom hooks
│   ├── utils/          # Helper functions
│   ├── styles/         # CSS/SCSS files
│   ├── App.jsx
│   └── main.jsx
├── package.json
└── vite.config.js
```

## Essential Tools

| Tool | Purpose |
|------|---------|
| React DevTools | Browser extension for debugging component tree and profiling |
| ESLint | Catch bugs and enforce code quality |
| Prettier | Consistent code formatting |
| TypeScript | Type safety (strongly recommended) |
| Storybook | Isolated component development and documentation |
| Vitest | Unit and integration testing |
| Playwright | End-to-end testing |

**Links**: [[React]] | [[Vite and esbuild]] | [[Next.js and Modern Web Frameworks]] | [[TypeScript]] | [[Web Development Fundamentals]]
