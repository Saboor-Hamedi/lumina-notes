---
id: a1b2c3d4-1034-4000-8000-000000000034
title: React Advanced Patterns
language: markdown
tags: [react, patterns, advanced]
selection: null
isPinned: false
customIcon: null
timestamp: 1782003600005
---

# React Advanced Patterns

**Links**: [[React Components Deep Dive]] | [[React Hook Mastery]] | [[React Performance Optimization]]

## Higher-Order Components (HOC)

```jsx
function withAuth(WrappedComponent) {
  return function AuthenticatedComponent(props) {
    const isAuthenticated = useAuth();

    if (!isAuthenticated) {
      return <Redirect to="/login" />;
    }

    return <WrappedComponent {...props} />;
  };
}

const ProtectedDashboard = withAuth(Dashboard);
```

## Render Props

```jsx
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMove = (e) => setPosition({ x: e.clientX, y: e.clientY });
    window.addEventListener('mousemove', handleMove);
    return () => window.removeEventListener('mousemove', handleMove);
  }, []);

  return render(position);
}

<MouseTracker render={({ x, y }) => (
  <div>Mouse at: {x}, {y}</div>
)} />
```

## Compound Components

```jsx
const SelectContext = createContext();

function Select({ children }) {
  const [selected, setSelected] = useState(null);
  return (
    <SelectContext.Provider value={{ selected, setSelected }}>
      <div className="select">{children}</div>
    </SelectContext.Provider>
  );
}

function Option({ value, children }) {
  const { selected, setSelected } = useContext(SelectContext);
  return (
    <div
      className={`option ${selected === value ? 'active' : ''}`}
      onClick={() => setSelected(value)}
    >
      {children}
    </div>
  );
}

<Select>
  <Option value="1">Option 1</Option>
  <Option value="2">Option 2</Option>
</Select>
```

**Links**: [[React Components Deep Dive]] | [[React Hook Mastery]] | [[React Best Practices and Projects]]
