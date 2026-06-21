---
id: a1b2c3d4-1035-4000-8000-000000000035
title: React Performance Optimization
language: markdown
tags: [react, performance]
selection: null
isPinned: false
customIcon: null
timestamp: 1782003600006
---

# React Performance Optimization

**Links**: [[React Hook Mastery]] | [[React Advanced Patterns]] | [[React Best Practices and Projects]]

## React.memo

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

```jsx
import { lazy, Suspense } from 'react';

const Dashboard = lazy(() => import('./Dashboard'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Dashboard />
    </Suspense>
  );
}
```

## Virtualization for Long Lists

```jsx
import { FixedSizeList } from 'react-window';

function VirtualizedList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>{items[index]}</div>
  );

  return (
    <FixedSizeList height={600} itemCount={items.length} itemSize={35} width="100%">
      {Row}
    </FixedSizeList>
  );
}
```

## Debouncing Search

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

**Links**: [[React Hook Mastery]] | [[React Advanced Patterns]] | [[React Best Practices and Projects]]
