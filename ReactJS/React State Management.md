---
id: a1b2c3d4-1032-4000-8000-000000000032
title: React State Management
language: markdown
tags: [react, state]
selection: null
isPinned: false
customIcon: null
timestamp: 1782003600003
---

# React State Management

**Links**: [[React Hook Mastery]] | [[React Core Concepts]] | [[State Management Patterns]]

## useState Hook

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

## Complex State with Objects

Always use the functional updater to merge correctly:

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

## Lifting State Up

When multiple components need the same state, lift it to the parent:

```jsx
function Parent() {
  const [sharedState, setSharedState] = useState('');

  return (
    <>
      <ChildA value={sharedState} onChange={setSharedState} />
      <ChildB value={sharedState} />
    </>
  );
}
```

**Links**: [[React Hook Mastery]] | [[React Components Deep Dive]] | [[React Advanced Patterns]]
