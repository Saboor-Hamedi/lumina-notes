---
id: a1b2c3d4-1026-4000-8000-000000000026
title: React Component Patterns
language: markdown
tags: [web-dev, react, patterns]
selection: null
isPinned: false
customIcon: null
timestamp: 1782000600001
---

# React Component Patterns

**Links**: [[React]] | [[React Hooks Deep Dive]] | [[State Management Patterns]] | [[Micro-Frontends]]

## Component Composition

Compose small components into larger ones using the `children` prop:

```jsx
function Layout({ children }) {
  return (
    <div className="layout">
      <Header />
      <main>{children}</main>
      <Footer />
    </div>
  );
}

<Layout>
  <Dashboard />
</Layout>
```

## Presentational vs Container Components

Separate UI from logic:

```jsx
// Presentational — pure UI, no logic
function UserCard({ name, email }) {
  return (
    <div className="card">
      <h3>{name}</h3>
      <p>{email}</p>
    </div>
  );
}

// Container — handles data fetching and state
function UserContainer({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, [userId]);

  return user ? <UserCard {...user} /> : <Loading />;
}
```

## Higher-Order Components (HOC)

A function that takes a component and returns an enhanced component:

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

Share logic by passing a render function as a prop:

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

Components that work together implicitly through shared context:

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

## Controlled vs Uncontrolled Components

| Aspect | Controlled | Uncontrolled |
|--------|-----------|-------------|
| State source | React state | DOM ref |
| Value | `value` + `onChange` | `ref` + `defaultValue` |
| Flexibility | Full control | Simpler for forms |

```jsx
// Controlled
function ControlledInput() {
  const [value, setValue] = useState('');
  return <input value={value} onChange={(e) => setValue(e.target.value)} />;
}

// Uncontrolled
function UncontrolledInput() {
  const ref = useRef();
  const handleSubmit = () => console.log(ref.current.value);
  return <input ref={ref} defaultValue="initial" />;
}
```

**Links**: [[React]] | [[React Hooks Deep Dive]] | [[State Management Patterns]] | [[Web Components]] | [[Micro-Frontends]]
