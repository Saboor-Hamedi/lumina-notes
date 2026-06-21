---
id: a1b2c3d4-1027-4000-8000-000000000027
title: React Performance Optimization
language: markdown
tags: [web-dev, react, performance]
selection: null
isPinned: false
customIcon: null
timestamp: 1782000600002
---

# React Performance Optimization

**Links**: [[React]] | [[React Hooks Deep Dive]] | [[Performance Testing]] | [[Vite and esbuild]] | [[Next.js and Modern Web Frameworks]]

## React.memo — Prevent Unnecessary Renders

Wraps a component to skip re-rendering when props haven't changed (shallow comparison):

```jsx
const ExpensiveList = React.memo(function ExpensiveList({ items }) {
  return (
    <ul>
      {items.map(item => <ListItem key={item.id} item={item} />)}
    </ul>
  );
});
```

## Code Splitting with Lazy + Suspense

Split your bundle so users only download what they need:

```jsx
import { lazy, Suspense } from 'react';

const Dashboard = lazy(() => import('./Dashboard'));
const Settings = lazy(() => import('./Settings'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Dashboard />
    </Suspense>
  );
}
```

## Virtualization for Long Lists

Render only visible rows with react-window:

```jsx
import { FixedSizeList } from 'react-window';

function VirtualizedList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>{items[index]}</div>
  );

  return (
    <FixedSizeList
      height={600}
      itemCount={items.length}
      itemSize={35}
      width="100%"
    >
      {Row}
    </FixedSizeList>
  );
}
```

## Debouncing Expensive Operations

Avoid firing expensive operations on every keystroke:

```jsx
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

function SearchBox() {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 300);

  useEffect(() => {
    // Perform search with debouncedQuery
  }, [debouncedQuery]);

  return <input value={query} onChange={(e) => setQuery(e.target.value)} />;
}
```

## Common Anti-Patterns

| Anti-Pattern | Why It Hurts | Fix |
|-------------|-------------|-----|
| Inline objects in render | New reference every render → child re-renders | Extract to constant or useMemo |
| Inline functions in render | Same as above | useCallback |
| Missing keys in lists | Full re-render instead of move | Stable, unique `key` prop |
| Lifting state too high | Unnecessary re-renders | Keep state close to where it's used |
| Overusing Context | Entire tree re-renders on any change | Split contexts, use Zustand/Jotai |

## Profiling with React DevTools

```bash
# Install React DevTools browser extension
# In React DevTools Profiler tab:
# 1. Click record
# 2. Interact with your app
# 3. Stop recording
# 4. Inspect flamegraph for slow renders
```

**Links**: [[React]] | [[React Hooks Deep Dive]] | [[Vite and esbuild]] | [[Next.js and Modern Web Frameworks]] | [[State Management Patterns]]
