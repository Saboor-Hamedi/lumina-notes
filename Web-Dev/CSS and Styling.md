---
id: a1b2c3d4-1025-4000-8000-000000000025
title: CSS and Styling
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001025
---

**Links**: [[Web Development Fundamentals]] | [[React]] | [[Web Accessibility]] | [[Angular]] | [[Vue.js]] | [[Web Components]]


# CSS and Styling

CSS controls the visual presentation of web content. Modern CSS offers powerful layout tools, animations, and responsive design capabilities.

## Box Model

```
┌───── Margin ───────────────────┐
│  ┌─── Border ────────────────┐ │
│  │  ┌─ Padding ────────────┐ │ │
│  │  │  ┌─ Content ───────┐ │ │ │
│  │  │  │                 │ │ │ │
│  │  │  └─────────────────┘ │ │ │
│  │  └──────────────────────┘ │ │
│  └───────────────────────────┘ │
└────────────────────────────────┘
```

## Layout Systems

| Method | Use Case |
|--------|----------|
| Flexbox | 1D layout (row or column) |
| Grid | 2D layout (rows + columns) |
| Float | Wrapping text around images (legacy) |
| Position | Absolute/relative positioning |
| Multi-column | Text columns (newspaper style) |

## Flexbox

```css
.container {
  display: flex;
  justify-content: center;   /* main axis */
  align-items: center;       /* cross axis */
  flex-wrap: wrap;
  gap: 16px;
}

.item {
  flex: 1 1 200px;  /* grow shrink basis */
}
```

## Grid

```css
.container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 24px;
}

.header {
  grid-column: 1 / -1;  /* span full width */
}
```

## Responsive Design

```css
/* Mobile-first approach */
.container { padding: 16px; }

@media (min-width: 768px) {
  .container { padding: 32px; }
}

@media (min-width: 1200px) {
  .container { max-width: 1200px; margin: 0 auto; }
}
```

## CSS Custom Properties

```css
:root {
  --primary: #3b82f6;
  --spacing-md: 16px;
}

.button {
  background: var(--primary);
  padding: var(--spacing-md);
}
```

## Modern Features

| Feature | Code |
|---------|------|
| Container queries | `@container (min-width: 400px)` |
| Nesting | `.card { & .title { } }` |
| Layers | `@layer base, components, utilities;` |
| Has selector | `.card:has(img)` |
| Scroll snap | `scroll-snap-type: x mandatory` |

## CSS-in-JS Approaches

| Approach | Example |
|----------|---------|
| Tailwind CSS | Utility-first classes |
| CSS Modules | Scoped class names |
| Styled Components | Tagged template literals |
| CSS-in-JS (vanilla-extract) | Zero-runtime CSS |

**Links**: [[Web Development Fundamentals]] | [[React]] | [[Web Security]] | [[HTTP Protocol]] | [[WebAssembly]]
