---
id: a1b2c3d4-1132-4000-8000-000000000132
title: Web Components
language: markdown
tags:
  - web-dev
  - web-components
  - frontend
selection: null
isPinned: false
timestamp: 1781832136825
---

**Links**: [[Micro-Frontends]] | [[Web Development Fundamentals]] | [[React]] | [[Angular]] | [[Vue.js]] | [[Web Accessibility]]


# Web Components

Web Components are a set of browser-native APIs for creating reusable, encapsulated custom HTML elements. They work across all frameworks and libraries.

## Three Core Technologies

| API | Purpose |
|-----|---------|
| Custom Elements | Define new HTML tags |
| Shadow DOM | Encapsulate styles and markup |
| HTML Templates | Declare fragments of markup |

## Custom Elements

```javascript
// Define a custom element
class UserCard extends HTMLElement {
    constructor() {
        super();
        this.attachShadow({ mode: "open" });
    }

    static get observedAttributes() {
        return ["name", "email", "avatar"];
    }

    connectedCallback() {
        this.render();
    }

    attributeChangedCallback(name, oldValue, newValue) {
        if (oldValue !== newValue) this.render();
    }

    render() {
        this.shadowRoot.innerHTML = `
            <style>
                .card {
                    border: 1px solid #ddd;
                    padding: 16px;
                    border-radius: 8px;
                    display: flex;
                    align-items: center;
                    gap: 12px;
                }
            </style>
            <div class="card">
                <img src="${this.getAttribute("avatar")}" alt="" width="48" height="48">
                <div>
                    <strong>${this.getAttribute("name")}</strong>
                    <p>${this.getAttribute("email")}</p>
                </div>
            </div>
        `;
    }
}

customElements.define("user-card", UserCard);
```

```html
<user-card name="Alice" email="alice@example.com" avatar="alice.jpg"></user-card>
```

## Lifecycle Callbacks

| Callback | When It Fires |
|----------|---------------|
| constructor() | Element created |
| connectedCallback() | Added to DOM |
| disconnectedCallback() | Removed from DOM |
| attributeChangedCallback() | Observed attribute changes |
| adoptedCallback() | Moved to new document |

## Shadow DOM Encapsulation

```javascript
class Tooltip extends HTMLElement {
    constructor() {
        super();
        // Shadow DOM — styles and markup are isolated
        this.attachShadow({ mode: "open" });
        this.shadowRoot.innerHTML = `
            <style>
                /* Only applies within this component */
                .tooltip { background: #333; color: white; padding: 4px 8px; border-radius: 4px; }
            </style>
            <slot></slot>
            <div class="tooltip" hidden>${this.getAttribute("text")}</div>
        `;
    }
}
customElements.define("my-tooltip", Tooltip);
```

**Modes**: `open` (accessible via `element.shadowRoot`) vs `closed` (not accessible externally).

## HTML Templates and Slots

```html
<template id="user-card-template">
    <style>
        .card { border: 1px solid var(--card-border, #ddd); }
        ::slotted(img) { border-radius: 50%; }
    </style>
    <div class="card">
        <slot name="avatar">
            <img src="default.png" alt="">
        </slot>
        <div>
            <slot name="name">Unknown</slot>
        </div>
    </div>
</template>

<script>
class UserCard extends HTMLElement {
    constructor() {
        super();
        const template = document.getElementById("user-card-template");
        this.attachShadow({ mode: "open" })
            .appendChild(template.content.cloneNode(true));
    }
}
customElements.define("user-card", UserCard);
</script>

<!-- Usage -->
<user-card>
    <img slot="avatar" src="alice.jpg">
    <span slot="name">Alice</span>
</user-card>
```

## Events

```javascript
class CounterWidget extends HTMLElement {
    constructor() {
        super();
        this._count = 0;
    }

    increment() {
        this._count++;
        this.dispatchEvent(new CustomEvent("count-change", {
            detail: { count: this._count },
            bubbles: true,
            composed: true // crosses shadow boundary
        }));
    }
}
customElements.define("counter-widget", CounterWidget);
```

## Framework Interop

Web Components work in any framework:

```javascript
// React (with ref)
function App() {
    const ref = useRef();
    useEffect(() => {
        ref.current.addEventListener("count-change", (e) => {
            console.log(e.detail.count);
        });
    }, []);
    return <counter-widget ref={ref} />;
}

// Vue
<template>
    <counter-widget @count-change="handleChange" />
</template>

// Angular
@Component({
    template: `<counter-widget (count-change)="handleChange($event)"></counter-widget>`
})
```

## Libraries

| Library | Description |
|---------|-------------|
| Lit | Google's lightweight Web Components library |
| Stencil | Compiler that generates standards-compliant WC |
| Haunted | React-like hooks for Web Components |
| FAST | Microsoft's web component framework |

**Links**: [[React]] | [[CSS and Styling]] | [[Web Development Fundamentals]] | [[Micro-Frontends]] | [[JavaScript]]
