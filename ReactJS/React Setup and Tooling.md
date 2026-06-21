---
id: 67889f9f-e6e5-416d-a21c-ba1e647b4345
title: React Setup and Tooling
language: markdown
tags: ''
selection: null
isPinned: false
customIcon: null
timestamp: 1782004645271
---

# React Setup and Tooling

**Links**: [[React Core Concepts]] | [[Vite and esbuild]] | [[TypeScript]] | [[Web Development Fundamentals]]

## Prerequisites

Before starting, ensure you have installed:
- **Node.js** (v18 or later) — [nodejs.org](https://nodejs.org)
- **npm** or **yarn** (comes with Node.js)
- A code editor — VS Code is recommended

Verify your installation:

```bash
node --version
npm --version
```

## 1. Create a Vite Project (Recommended)

Vite is the modern, fast build tool for React. This is the recommended way to start a new React project.

```bash
npm create vite@latest my-app -- --template react
cd my-app
npm install
npm run dev
```

Open `http://localhost:5173` in your browser.

For TypeScript:

```bash
npm create vite@latest my-app -- --template react-ts
```

## 2. Install Tailwind CSS

Tailwind is a utility-first CSS framework that pairs perfectly with React.

### For Vite + React:

```bash
npm install -D tailwindcss @tailwindcss/vite
```

Add the plugin to `vite.config.js`:

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [react(), tailwindcss()],
})
```

Add to your main CSS file (`src/index.css` or `src/App.css`):

```css
@import "tailwindcss";
```

### Verify Tailwind works:

```jsx
// src/App.jsx
function App() {
  return (
    <div className="text-3xl font-bold text-blue-600">
      Hello Tailwind + React!
    </div>
  );
}

export default App;
```

Run `npm run dev` and you should see styled text.

## 3. Alternative: Create React App (Legacy)

CRA is in maintenance mode. Prefer Vite for new projects.

```bash
npx create-react-app my-app
cd my-app
npm start
```

### Tailwind with CRA:

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

Update `tailwind.config.js`:

```js
/** @type {import('tailwindcss').Config} */
export default {
  content: ["./index.html", "./src/**/*.{js,jsx}"],
  theme: { extend: {} },
  plugins: [],
}
```

Add to `src/index.css`:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

## 4. Alternative: Next.js (Full-Stack React)

```bash
npx create-next-app@latest my-app
cd my-app
npm run dev
```

### Tailwind with Next.js:

Next.js 15+ ships with Tailwind pre-configured when you select it during `create-next-app`. If not:

```bash
npm install -D tailwindcss @tailwindcss/postcss
```

Create `postcss.config.mjs`:

```js
/** @type {import('postcss-load-config').Config} */
const config = {
  plugins: {
    "@tailwindcss/postcss": {},
  },
};
export default config;
```

Add to `src/app/globals.css`:

```css
@import "tailwindcss";
```

## 5. Install React Router (For Multi-Page Apps)

```bash
npm install react-router-dom
```

Basic setup:

```jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import About from './pages/About';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}
```

## 6. Install Other Common Tools

```bash
# State management
npm install zustand                # Simple global state

# Server state (data fetching + caching)
npm install @tanstack/react-query

# Form handling
npm install react-hook-form

# Icons
npm install lucide-react

# Date handling
npm install date-fns
```

## Project Structure (Vite + Tailwind)

```
my-app/
├── public/
│   └── index.html
├── src/
│   ├── components/        # Reusable components
│   │   ├── ui/            # Base UI (Button, Input, Modal)
│   │   └── layout/        # Layout (Header, Sidebar, Footer)
│   ├── pages/             # Page components
│   ├── hooks/             # Custom hooks
│   ├── utils/             # Helper functions
│   ├── services/          # API calls
│   ├── styles/
│   │   └── index.css      # Tailwind imports
│   ├── App.jsx
│   └── main.jsx
├── package.json
├── vite.config.js
└── tailwind.config.js
```

## Quick Start Checklist

- [ ] Node.js installed (`node --version`)
- [ ] Vite project created (`npm create vite@latest`)
- [ ] Dependencies installed (`npm install`)
- [ ] Tailwind installed and configured
- [ ] Dev server running (`npm run dev`)
- [ ] Browser opens to `http://localhost:5173`
- [ ] Optional: React Router, Zustand, React Query installed

**Links**: [[React Core Concepts]] | [[Vite and esbuild]] | [[Next.js and Modern Web Frameworks]] | [[TypeScript]] | [[CSS and Styling]]
