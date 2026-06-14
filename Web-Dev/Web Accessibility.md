---
id: a1b2c3d4-1148-4000-8000-000000000148
title: Web Accessibility
language: markdown
tags: [web-dev, a11y, accessibility]
selection: null
isPinned: false
timestamp: 1781500001148
---

**Links**: [[CSS and Styling]] | [[Web Development Fundamentals]] | [[React]] | [[Web Components]] | [[Angular]] | [[Progressive Web Apps]]


# Web Accessibility (a11y)

Web accessibility ensures that people with disabilities can perceive, understand, navigate, and interact with the web. It's both an ethical responsibility and a legal requirement.

## WCAG Guidelines

WCAG 2.1 has four principles (POUR):

| Principle | Description |
|-----------|-------------|
| Perceivable | Users must be able to perceive content |
| Operable | Users must be able to operate interfaces |
| Understandable | Users must be able to understand content |
| Robust | Content must work with assistive technologies |

## Conformance Levels

| Level | Description | Target |
|-------|-------------|--------|
| A | Minimum, must-have | Essential barriers removed |
| AA | Standard (legal benchmark) | Most common standard |
| AAA | Highest, best experience | Not required for all content |

## Semantic HTML

```html
<!-- Bad -->
<div class="nav">
    <div class="item" onclick="navigate('/home')">Home</div>
</div>

<!-- Good -->
<nav aria-label="Main navigation">
    <a href="/home">Home</a>
    <a href="/about">About</a>
</nav>
```

**Semantic elements**: `<header>`, `<nav>`, `<main>`, `<article>`, `<section>`, `<aside>`, `<footer>`, `<button>`, `<form>`, `<table>`.

## ARIA (Accessible Rich Internet Applications)

```html
<!-- Only use ARIA when native semantics are insufficient -->
<button aria-expanded="false" aria-controls="menu" onclick="toggleMenu()">
    Menu
</button>

<div role="tablist">
    <button role="tab" aria-selected="true" aria-controls="panel1">Tab 1</button>
    <button role="tab" aria-selected="false" aria-controls="panel2">Tab 2</button>
</div>

<!-- Live region for dynamic updates -->
<div aria-live="polite" aria-atomic="true">
    Cart has {{count}} items
</div>
```

**First rule of ARIA**: Don't use ARIA if you can use a native HTML element.

## Keyboard Navigation

```javascript
// Ensure custom components are keyboard accessible
class CustomSelect extends HTMLElement {
    connectedCallback() {
        this.addEventListener("keydown", (e) => {
            switch(e.key) {
                case "ArrowDown":
                    this.focusNextOption();
                    break;
                case "ArrowUp":
                    this.focusPreviousOption();
                    break;
                case "Enter":
                case " ":
                    this.toggleExpanded();
                    break;
                case "Escape":
                    this.collapse();
                    break;
            }
        });
    }
}
```

**Tab order**: Use `tabindex` carefully:
- `tabindex="0"` → natural order (preferred)
- `tabindex="-1"` → programmatic focus only
- `tabindex="1+"` → avoid (breaks natural order)

## Focus Management

```javascript
// Manage focus for modals, Single Page App navigation
function openModal(modalId) {
    const modal = document.getElementById(modalId);
    modal.style.display = "block";

    // Trap focus inside modal
    modal.focus();

    // Return focus on close
    const trigger = document.activeElement;
    modal.addEventListener("keydown", (e) => {
        if (e.key === "Escape") {
            modal.style.display = "none";
            trigger.focus();  // Return focus
        }
    });
}
```

## Color and Contrast

```css
/* Minimum contrast ratios:
   AA: 4.5:1 (normal text), 3:1 (large text)
   AAA: 7:1 (normal text), 4.5:1 (large text) */

/* Don't rely on color alone */
.error {
    color: #d32f2f;
    /* Add icon or text indicator */
    &::before {
        content: "⚠ ";
    }
}

/* Focus indicators */
:focus-visible {
    outline: 2px solid #1976d2;
    outline-offset: 2px;
}
```

## Screen Reader Support

```html
<!-- Alt text for images -->
<img src="chart.png" alt="Bar chart showing Q1 revenue: $10M, Q2: $12M, Q3: $8M, Q4: $15M">

<!-- Decorative images -->
<img src="decoration.png" alt="" role="presentation">

<!-- Hidden descriptive text -->
<span class="sr-only">Close dialog</span>
<button aria-label="Close dialog">×</button>

<!-- Status updates -->
<div role="status">Search results loaded. 42 items found.</div>
```

## Forms

```html
<!-- Every input needs a label -->
<label for="email">Email address</label>
<input id="email" type="email" required aria-describedby="email-hint">
<span id="email-hint">We'll never share your email</span>

<!-- Error announcement -->
<input aria-invalid="true" aria-errormessage="email-error">
<span id="email-error" role="alert">Please enter a valid email address</span>

<!-- Grouping -->
<fieldset>
    <legend>Shipping address</legend>
    <!-- related inputs -->
</fieldset>
```

## Testing

| Test | Method | Tool |
|------|--------|------|
| Automated | CI pipeline scan | axe-core, Lighthouse |
| Keyboard | Manual tab through site | Browser |
| Screen reader | Manual listen test | NVDA, VoiceOver, JAWS |
| Color contrast | Automated check | Color Contrast Analyzer |
| Zoom | Manual 200% zoom test | Browser |

```javascript
// Automated testing (axe-core)
import axe from "axe-core";

afterEach(async () => {
    const results = await axe.run(document);
    expect(results.violations).toHaveLength(0);
});
```

## Common Violations

| Violation | Fix |
|-----------|-----|
| Missing alt text | Add `alt` attribute to all images |
| Low contrast | Check contrast ratios |
| Missing form labels | Add `<label>` for each input |
| Non-semantic buttons | Use `<button>` not `<div onclick>` |
| Keyboard trap | Ensure focus can leave all components |
| Missing focus indicator | Add visible `:focus-visible` styles |
| Empty headings | Remove or fill `<h1>`-`<h6>` |

**Links**: [[Web Development Fundamentals]] | [[CSS and Styling]] | [[React]] | [[Internationalization]] | [[Technical Writing]]
