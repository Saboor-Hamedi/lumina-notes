---
id: a1b2c3d4-1031-4000-8000-000000000031
title: React Components Deep Dive
language: markdown
tags: [react, components]
selection: null
isPinned: false
customIcon: null
timestamp: 1782003600002
---

# React Components Deep Dive

**Links**: [[React Core Concepts]] | [[React Advanced Patterns]] | [[React Hook Mastery]]

## Component Composition

Breaking UI into smaller pieces using the `children` prop:

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

```jsx
// Presentational — UI only, no logic
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

**Links**: [[React Core Concepts]] | [[React State Management]] | [[React Advanced Patterns]]
