---
id: a1b2c3d4-1133-4000-8000-000000000133
title: State Management Patterns
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001133
---
# State Management Patterns

State management handles how application data flows, changes, and is shared across components. Different patterns suit different application scales.

## Local State (Component State)

```javascript
// React useState — local to component
function Counter() {
    const [count, setCount] = useState(0);
    return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

**Best for**: Form inputs, toggle states, UI-specific data.

## Lifting State Up

```javascript
function Parent() {
    const [value, setValue] = useState("");

    return (
        <>
            <Input value={value} onChange={setValue} />
            <Display value={value} />
        </>
    );
}
```

**Best for**: Sharing state between sibling components at the same level.

## Context / Provider Pattern

```javascript
// React Context
const ThemeContext = createContext("light");

function App() {
    const [theme, setTheme] = useState("light");
    return (
        <ThemeContext.Provider value={{ theme, setTheme }}>
            <Toolbar />
        </ThemeContext.Provider>
    );
}

function ThemedButton() {
    const { theme, setTheme } = useContext(ThemeContext);
    return <button className={theme}>Click</button>;
}
```

**Best for**: Theming, localization, auth state, global preferences.

**Caveat**: Avoid overuse — context re-renders all consumers on any change.

## Flux / Unidirectional Data Flow

```
Action → Dispatcher → Store → View
                 ↑_______________|
```

Characteristics:
- Data flows in one direction
- State is read-only (immutable updates)
- Changes are triggered by actions

## Redux Pattern

```javascript
// Actions
const ADD_TODO = "ADD_TODO";
const addTodo = (text) => ({ type: ADD_TODO, payload: text });

// Reducer
function todosReducer(state = [], action) {
    switch (action.type) {
        case ADD_TODO:
            return [...state, { id: Date.now(), text: action.payload }];
        default:
            return state;
    }
}

// Store
const store = createStore(todosReducer);
store.dispatch(addTodo("Learn Redux"));
```

Modern alternative: Redux Toolkit

```javascript
import { createSlice, configureStore } from "@reduxjs/toolkit";

const todosSlice = createSlice({
    name: "todos",
    initialState: [],
    reducers: {
        add: (state, action) => { state.push({ id: Date.now(), text: action.payload }); },
        toggle: (state, action) => { const t = state.find(t => t.id === action.payload); if (t) t.done = !t.done; }
    }
});

const store = configureStore({ reducer: todosSlice.reducer });
```

## Zustand (Simpler Alternative)

```javascript
import { create } from "zustand";

const useStore = create((set) => ({
    count: 0,
    increment: () => set((state) => ({ count: state.count + 1 })),
    decrement: () => set((state) => ({ count: state.count - 1 })),
}));

function Counter() {
    const { count, increment } = useStore();
    return <button onClick={increment}>{count}</button>;
}
```

## State Machine Pattern

```javascript
import { createMachine, interpret } from "xstate";

const toggleMachine = createMachine({
    id: "toggle",
    initial: "inactive",
    states: {
        inactive: { on: { TOGGLE: "active" } },
        active: { on: { TOGGLE: "inactive" } }
    }
});

const service = interpret(toggleMachine).onTransition((state) => {
    console.log(state.value);
});
service.start();
service.send("TOGGLE"); // → "active"
```

**Best for**: Complex UI flows, multi-step forms, async workflows.

## Server State (React Query / TanStack Query)

```javascript
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";

function Posts() {
    const { data, isLoading } = useQuery({
        queryKey: ["posts"],
        queryFn: () => fetch("/api/posts").then(r => r.json())
    });

    const queryClient = useQueryClient();
    const mutation = useMutation({
        mutationFn: (newPost) => fetch("/api/posts", { method: "POST", body: JSON.stringify(newPost) }),
        onSuccess: () => queryClient.invalidateQueries({ queryKey: ["posts"] })
    });

    return <button onClick={() => mutation.mutate({ title: "New" })}>Add</button>;
}
```

## Pattern Selection Guide

| Pattern | Scale | Complexity | Best For |
|---------|-------|------------|----------|
| Local state | Tiny | Low | Simple components |
| Lifting state | Small | Low | Sibling communication |
| Context | Medium | Medium | Global settings |
| Redux/Zustand | Large | Medium-High | Complex data flows |
| State machines | Medium | Medium | Complex UI logic |
| Server state | Any | Medium | API data caching |

## URL as State

```javascript
import { useSearchParams } from "react-router-dom";

function SearchPage() {
    const [searchParams, setSearchParams] = useSearchParams();
    const query = searchParams.get("q") || "";

    return (
        <input
            value={query}
            onChange={(e) => setSearchParams({ q: e.target.value })}
        />
    );
}
```

**Best for**: Shareable URLs, pagination, filters, search queries.

**Links**: [[React]] | [[Web Development Fundamentals]] | [[Event-Driven Architecture]] | [[Architecture Patterns]] | [[Functional Programming Concepts]] | [[Vue.js]] | [[Angular]] | [[Svelte]] | [[GoF Design Patterns]]

**See also**: [[Observer Pattern]], [[Micro-Frontends]], [[API Gateway Patterns]], [[Database Transactions]] (ACID vs state consistency)
