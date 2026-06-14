---
id: a1b2c3d4-1110-4000-8000-000000000110
title: Micro-Frontends
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001110
---

**Links**: [[Web Components]] | [[Angular]] | [[React]] | [[Vue.js]] | [[State Management Patterns]] | [[Next.js and Modern Web Frameworks]]


# Micro-Frontends

Micro-frontends extend microservices principles to the frontend — independently developed, tested, and deployed UI pieces that compose into a cohesive application.

## Why Micro-Frontends?

| Problem | Solution |
|---------|----------|
| Monolithic frontend becomes unmanageable | Split into smaller, independent pieces |
| Teams can't deploy independently | Each team owns its frontend module |
| Tech stack lock-in | Each micro-frontend uses its own framework |
| Scaling the development team | Teams work in parallel on separate modules |

## Integration Approaches

### 1. Build-Time (Module Federation)

```javascript
// host/webpack.config.js
const { ModuleFederationPlugin } = require("webpack").container;

new ModuleFederationPlugin({
  name: "shell",
  remotes: {
    products: "products@http://localhost:3001/remoteEntry.js",
    cart: "cart@http://localhost:3002/remoteEntry.js",
  },
  shared: {
    react: { singleton: true, requiredVersion: "^18.0.0" },
    "react-dom": { singleton: true },
  },
});
```

**Pros**: Shared deps, version alignment. **Cons**: Coupling, coordinated deploys.

### 2. Run-Time (Web Components)

```html
<micro-frontend name="products" url="http://products-app.example.com"></micro-frontend>

<script>
class MicroFrontend extends HTMLElement {
  connectedCallback() {
    fetch(this.getAttribute("url"))
      .then(r => r.text())
      .then(html => { this.shadowRoot.innerHTML = html; });
  }
}
customElements.define("micro-frontend", MicroFrontend);
</script>
```

**Pros**: Independent deploys, any framework. **Cons**: Duplicate deps, slower.

### 3. Single SPA

```javascript
import { registerApplication, start } from "single-spa";
registerApplication({ name: "products", app: () => System.import("products"), activeWhen: ["/products"] });
registerApplication({ name: "cart", app: () => System.import("cart"), activeWhen: ["/cart"] });
start();
```

## Communication

| Method | Use Case |
|--------|----------|
| Custom events | One-way broadcast |
| Shared store | Global state |
| URL params | Navigation, simple data |
| Backend API | Complex/persistent state |

## When to Use

| Use | Don't Use |
|-----|-----------|
| Large team (50+) | Small team (< 10) |
| Multiple feature teams | Simple app |
| Mixed tech stacks | Uniform stack |
| Independent deploy cadences | Tight coupling |

**Links**: [[React]] | [[Microservices Architecture]] | [[Web Development Fundamentals]] | [[Vite and esbuild]] | [[Web Components]] | [[Docker Containers]] | [[CI CD Pipelines]]

**See also**: [[Monorepo with Nx and Turborepo]], [[Monorepo vs Polyrepo]], [[Dev Environment Setup]], [[State Management Patterns]]
