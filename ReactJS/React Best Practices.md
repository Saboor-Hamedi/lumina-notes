---
id: a1b2c3d4-1028-4000-8000-000000000028
title: React Best Practices
language: markdown
tags: [web-dev, react, best-practices]
selection: null
isPinned: false
customIcon: null
timestamp: 1782000600003
---

# React Best Practices

**Links**: [[React]] | [[React Hooks Deep Dive]] | [[React Component Patterns]] | [[React Performance Optimization]] | [[TypeScript]]

## Project Structure

```
src/
├── components/
│   ├── common/       # Shared components (Button, Input, Modal)
│   ├── layout/       # Layout components (Header, Sidebar, Footer)
│   └── features/     # Feature-specific components (Dashboard, Settings)
├── hooks/            # Custom hooks
├── contexts/         # React contexts
├── utils/            # Helper functions
├── services/         # API calls
├── styles/           # Global styles
└── types/            # TypeScript types
```

## Code Organization

- One component per file
- Co-locate tests, styles, and stories with the component
- Group by feature, not by type
- Export the component as default, types as named exports

## Performance Guidelines

- ✅ Use stable keys in lists (avoid index as key)
- ✅ Memoize expensive calculations with useMemo
- ✅ Lazy load routes and heavy components
- ✅ Implement proper cleanup in useEffect
- ✅ Use React.memo for pure presentational components
- ❌ Avoid inline object/function creation in render
- ❌ Don't mutate state directly
- ❌ Don't over-optimize prematurely

## Accessibility

- Use semantic HTML elements (`<nav>`, `<main>`, `<button>`)
- Add ARIA labels where semantics are insufficient
- Ensure keyboard navigation works (tabIndex, onKeyDown)
- Maintain proper color contrast
- Test with screen readers

## Testing Strategy

| Layer | Tool | Scope |
|-------|------|-------|
| Unit | Vitest + React Testing Library | Component logic, hooks |
| Integration | React Testing Library | Component interactions |
| E2E | Playwright or Cypress | Full user flows |
| Visual regression | Chromatic or Percy | UI snapshots |

```jsx
// Example: testing a component
import { render, screen, fireEvent } from '@testing-library/react';
import Counter from './Counter';

test('increments count', () => {
  render(<Counter />);
  fireEvent.click(screen.getByText('Increment'));
  expect(screen.getByText('Count: 1')).toBeInTheDocument();
});
```

## State Management Decision

| Scale | Approach |
|-------|----------|
| Component-local | useState / useReducer |
| Feature-level | Context + custom hooks |
| App-wide | Zustand, Jotai, or Redux Toolkit |
| Server state | TanStack Query (React Query) |
| URL state | Next.js router / React Router |

## Common Pitfalls

- **Missing dependency arrays** in useEffect → stale closures
- **Mutating state directly** → no re-render
- **Over-nesting providers** → hard to debug
- **Giant useEffect** → split into smaller effects
- **Not handling loading/error states** → poor UX

**Links**: [[React]] | [[TypeScript]] | [[Web Accessibility]] | [[Web Development Fundamentals]] | [[Testing]]
