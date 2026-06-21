---
id: a1b2c3d4-1024-4000-8000-000000000024
title: React
language: markdown
tags: [web-dev, react, frontend]
selection: null
isPinned: false
timestamp: 1781500001024
---

**Links**: [[Angular]] | [[Vue.js]] | [[Svelte]] | [[Next.js and Modern Web Frameworks]] | [[State Management Patterns]] | [[Vite and esbuild]] | [[React Hooks Deep Dive]] | [[React Component Patterns]] | [[React Performance Optimization]] | [[React Best Practices]] | [[React Setup and Tooling]]


# React

React is a declarative, component-based UI library for building interactive user interfaces.

## Core Concepts

| Concept | Description |
|---------|-------------|
| Components | Reusable UI building blocks |
| JSX | JavaScript XML syntax extension |
| Props | Read-only data passed to components |
| State | Mutable data that triggers re-renders |
| Effects | Side effects (data fetching, subscriptions) |
| Context | Global state without prop drilling |

## Functional Components

```jsx
function Greeting({ name, age }) {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      <p>Age: {age}</p>
    </div>
  );
}
```

## Hooks

| Hook | Purpose |
|------|---------|
| `useState` | Local state |
| `useEffect` | Side effects + lifecycle |
| `useRef` | Mutable refs, DOM access |
| `useMemo` | Memoized values |
| `useCallback` | Memoized functions |
| `useContext` | Consume context |
| `useReducer` | Complex state logic |

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);

  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

## State Management

| Solution | Best For |
|----------|----------|
| useState / useReducer | Local component state |
| Context + useReducer | Medium-scale app state |
| Zustand | Simple global state |
| Redux Toolkit | Large-scale, complex state |
| React Query / TanStack Query | Server state (caching, sync) |
| Jotai / Recoil | Atomic state (fine-grained) |

## Component Patterns

```jsx
// Compound components
<Select>
  <Select.Option value="a">Option A</Select.Option>
  <Select.Option value="b">Option B</Select.Option>
</Select>

// Render props
<DataFetcher url="/api/users">
  {({ data, loading }) =>
    loading ? <Spinner /> : <UserList users={data} />
  }
</DataFetcher>

// Custom hook
function useWindowSize() {
  const [size, setSize] = useState({ width: 0, height: 0 });
  useEffect(() => {
    const handler = () => setSize({ width: window.innerWidth, height: window.innerHeight });
    window.addEventListener("resize", handler);
    return () => window.removeEventListener("resize", handler);
  }, []);
  return size;
}
```

## Performance Optimization

- `React.memo` — skip re-render if props unchanged
- `useMemo` / `useCallback` — stable references
- `useTransition` — mark non-urgent updates
- Virtualization for long lists (react-window)
- Code splitting with `React.lazy` + `Suspense`

**Links**: [[TypeScript]] | [[Web Development Fundamentals]] | [[REST API Design]] | [[Build Tools]] | [[Async Python]] | [[State Management Patterns]] | [[Next.js and Modern Web Frameworks]] | [[Vite and esbuild]] | [[Web Accessibility]]

**See also**: [[Unit Testing Guide]], [[Performance Testing]], [[Micro-Frontends]], [[Web Components]], [[Web Accessibility]]

## Deep Dives

- [[React Hooks Deep Dive]] — useState, useEffect, useContext, custom hooks, useMemo, useCallback, useReducer
- [[React Component Patterns]] — composition, HOCs, render props, compound components, controlled vs uncontrolled
- [[React Performance Optimization]] — memo, code splitting, virtualization, debouncing, profiling
- [[React Best Practices]] — project structure, testing, accessibility, state management decisions
- [[React Setup and Tooling]] — Vite, CRA, Next.js, essential tools
