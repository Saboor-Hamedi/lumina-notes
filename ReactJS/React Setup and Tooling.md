---
id: a1b2c3d4-1029-4000-8000-000000000029
title: React Setup and Tooling
language: markdown
tags: [react, setup, tooling]
selection: null
isPinned: false
customIcon: null
timestamp: 1782003600000
---

# React Setup and Tooling

**Links**: [[React Core Concepts]] | [[Vite and esbuild]] | [[TypeScript]] | [[Web Development Fundamentals]]

## Vite (Recommended)

```bash
npm create vite@latest my-app -- --template react
cd my-app
npm install
npm run dev
```

For TypeScript: `-- --template react-ts`

## Create React App (Traditional)

```bash
npx create-react-app my-app
cd my-app
npm start
```

## Next.js (Full-Stack)

```bash
npx create-next-app@latest my-app
cd my-app
npm run dev
```

## Project Structure

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

**Links**: [[React Core Concepts]] | [[Vite and esbuild]] | [[Next.js and Modern Web Frameworks]] | [[TypeScript]]
