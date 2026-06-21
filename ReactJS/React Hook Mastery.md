---
id: a1b2c3d4-1033-4000-8000-000000000033
title: React Hook Mastery
language: markdown
tags: [react, hooks]
selection: null
isPinned: false
customIcon: null
timestamp: 1782003600004
---

# React Hook Mastery

**Links**: [[React Core Concepts]] | [[React State Management]] | [[React Performance Optimization]]

## useEffect — Side Effects

Handles data fetching, subscriptions, and DOM manipulation. Always return a cleanup function.

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
    <button className={theme} onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      Toggle Theme
    </button>
  );
}
```

## Custom Hooks

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
```

## useMemo & useCallback

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

**Links**: [[React State Management]] | [[React Performance Optimization]] | [[React Advanced Patterns]]
