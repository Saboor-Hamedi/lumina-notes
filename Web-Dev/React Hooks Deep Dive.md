---
id: a1b2c3d4-1025-4000-8000-000000000025
title: React Hooks Deep Dive
language: markdown
tags: [web-dev, react, hooks]
selection: null
isPinned: false
customIcon: null
timestamp: 1782000600000
---

# React Hooks Deep Dive

**Links**: [[React]] | [[State Management Patterns]] | [[Web Development Fundamentals]]

## useState — Local State

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}
```

### Complex State with Objects

Always use the functional updater to merge state correctly:

```jsx
function UserProfile() {
  const [user, setUser] = useState({ name: '', email: '', age: 0 });

  const updateField = (field, value) => {
    setUser(prev => ({ ...prev, [field]: value }));
  };

  return (
    <input
      value={user.name}
      onChange={(e) => updateField('name', e.target.value)}
    />
  );
}
```

## useEffect — Side Effects

Handles data fetching, subscriptions, DOM manipulation, and timers. Always return a cleanup function.

```jsx
function DataFetcher({ url }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    let cancelled = false;

    async function fetchData() {
      setLoading(true);
      try {
        const response = await fetch(url);
        const result = await response.json();
        if (!cancelled) setData(result);
      } catch (error) {
        console.error(error);
      } finally {
        if (!cancelled) setLoading(false);
      }
    }

    fetchData();

    return () => { cancelled = true; };
  }, [url]);

  return loading ? <Spinner /> : <Display data={data} />;
}
```

### Dependency Array Rules

| Dependency Array | When It Runs | Use Case |
|-----------------|-------------|----------|
| `undefined` | Every render | Debugging only (avoid) |
| `[]` | On mount, cleanup on unmount | Event listeners, subscriptions |
| `[a, b]` | When a or b change | Data fetching, derived effects |

## useContext — Global State

```jsx
const ThemeContext = createContext();

function App() {
  const [theme, setTheme] = useState('light');
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <MainContent />
    </ThemeContext.Provider>
  );
}

function ThemedButton() {
  const { theme, setTheme } = useContext(ThemeContext);
  return (
    <button
      className={theme}
      onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}
    >
      Toggle Theme
    </button>
  );
}
```

## Custom Hooks

Encapsulate reusable stateful logic:

```jsx
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initialValue;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

// Usage
function App() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
}
```

## useMemo & useCallback — Performance

```jsx
function ExpensiveComponent({ items, filter }) {
  const filteredItems = useMemo(
    () => items.filter(item => item.includes(filter)),
    [items, filter]
  );

  const handleClick = useCallback((id) => {
    console.log('Clicked:', id);
  }, []);

  return (
    <ul>
      {filteredItems.map(item => (
        <li key={item} onClick={() => handleClick(item)}>{item}</li>
      ))}
    </ul>
  );
}
```

## useReducer — Complex State Logic

```jsx
function reducer(state, action) {
  switch (action.type) {
    case 'increment': return { count: state.count + 1 };
    case 'decrement': return { count: state.count - 1 };
    case 'reset': return { count: 0 };
    default: return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </>
  );
}
```

**Links**: [[React]] | [[State Management Patterns]] | [[Web Accessibility]] | [[TypeScript]]
