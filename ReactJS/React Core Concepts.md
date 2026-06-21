---
id: a1b2c3d4-1030-4000-8000-000000000030
title: React Core Concepts
language: markdown
tags: [react, core-concepts]
selection: null
isPinned: false
customIcon: null
timestamp: 1782003600001
---

# React Core Concepts

**Links**: [[React Setup and Tooling]] | [[React Components Deep Dive]] | [[React Hook Mastery]] | [[Web Development Fundamentals]]

## What is React?

React is a JavaScript library for building user interfaces, developed by Facebook (Meta). It uses a component-based architecture and virtual DOM for efficient rendering.

### Why React?
- **Component-Based**: Reusable, modular UI pieces
- **Virtual DOM**: Efficient updates and rendering
- **Declarative**: Describe what UI should look like
- **Large Ecosystem**: Rich community and tools
- **Cross-Platform**: React Native for mobile

### Key Concepts
- **Components**: Building blocks of React apps
- **JSX**: Syntax extension for writing HTML in JavaScript
- **Props**: Data passed between components
- **State**: Internal data that changes over time
- **Hooks**: Functions to use state and lifecycle features

## JSX (JavaScript XML)

```jsx
const element = <h1>Hello, {name}!</h1>;
```

Rules:
- Must return single root element
- Use `className` instead of `class`
- Close all tags
- JavaScript expressions in `{}`

## Functional Components

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

// Arrow function
const Welcome = ({ name }) => <h1>Hello, {name}</h1>;
```

## Props (Properties)

```jsx
function App() {
  return <Welcome name="Saboor" age={25} />;
}

function Welcome({ name, age }) {
  return (
    <div>
      <h1>Hello, {name}</h1>
      <p>Age: {age}</p>
    </div>
  );
}
```

## Conditional Rendering

```jsx
function Greeting({ isLoggedIn }) {
  if (isLoggedIn) return <h1>Welcome back!</h1>;
  return <h1>Please sign in</h1>;
}

// Ternary
return isLoggedIn ? <LogoutButton /> : <LoginButton />;

// Short-circuit
return hasMessages && <MessageList />;
```

**Links**: [[React Setup and Tooling]] | [[React Components Deep Dive]] | [[React Hook Mastery]] | [[React Advanced Patterns]]
