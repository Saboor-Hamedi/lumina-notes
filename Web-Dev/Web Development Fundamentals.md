---
id: new-001-0000-0000-0000-000000000001
title: Web Development Fundamentals
language: markdown
tags: [web-dev, web-fundamentals]
selection: null
isPinned: false
timestamp: 1781600000001
---
# Web Development Fundamentals

**Links**: [[HTTP Protocol]] | [[REST API Design]] | [[Programming Language Paradigms]] | [[Dev Environment Setup]] | [[Browser Developer Tools]]

## The Web Triad

Every web page relies on three core technologies:

| Technology | Purpose | File Extension |
|------------|---------|----------------|
| **HTML** | Structure and content | `.html` |
| **CSS** | Presentation and layout | `.css` |
| **JavaScript** | Behavior and interactivity | `.js` |

## HTML Basics

HTML (HyperText Markup Language) uses tags to structure content.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>My Page</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>Welcome</h1>
    <nav>
      <a href="/about">About</a>
      <a href="/contact">Contact</a>
    </nav>
  </header>
  <main>
    <article>
      <h2>Article Title</h2>
      <p>This is a paragraph of text.</p>
    </article>
  </main>
  <footer>&copy; 2024 My Site</footer>
  <script src="app.js"></script>
</body>
</html>
```

## CSS Selectors

```css
/* Element selector */
h1 { color: blue; }

/* Class selector */
.card { border: 1px solid gray; }

/* ID selector */
#header { background: black; }

/* Descendant */
article p { line-height: 1.6; }

/* Pseudo-class */
button:hover { opacity: 0.8; }

/* Flexbox layout */
.container {
  display: flex;
  justify-content: center;
  gap: 1rem;
}
```

## DOM Manipulation (JavaScript)

```javascript
// Select elements
const button = document.querySelector('#submit-btn');
const form = document.getElementById('login-form');

// Event listener
button.addEventListener('click', (event) => {
  event.preventDefault();
  const email = document.querySelector('#email').value;
  console.log('Form submitted:', email);
});

// Create elements
const div = document.createElement('div');
div.textContent = 'Dynamic content';
document.body.appendChild(div);
```

**See also**: [[Progressive Web Apps]], [[Web Accessibility]], [[State Management Patterns]]

**Next**: [[Build Tools]] — Bundlers and task runners