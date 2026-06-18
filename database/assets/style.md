```css
/* ── Lumina Teaching Workspace: Shared Stylesheet ── */

@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap');

:root {
  --bg: #fafafa;
  --surface: #ffffff;
  --text: #1a1a2e;
  --text-secondary: #555;
  --accent: #2563eb;
  --accent-light: #dbeafe;
  --accent-dark: #1d4ed8;
  --border: #e5e7eb;
  --code-bg: #f3f4f6;
  --green: #059669;
  --green-bg: #d1fae5;
  --orange: #d97706;
  --orange-bg: #fef3c7;
  --red: #dc2626;
  --red-bg: #fee2e2;
  --warm: #f8f4ee;
  --hr: #e5e7eb;
}

* { box-sizing: border-box; margin: 0; padding: 0; }

body {
  font-family: 'Inter', -apple-system, system-ui, sans-serif;
  font-size: 16px;
  line-height: 1.7;
  color: var(--text);
  background: var(--bg);
  padding: 2rem 1rem;
  max-width: 800px;
  margin: 0 auto;
}

@media (max-width: 640px) {
  body { padding: 1.25rem 0.75rem; }
  pre { font-size: 13px; }
}

h1, h2, h3, h4 {
  font-weight: 600;
  line-height: 1.3;
  margin-top: 2rem;
  margin-bottom: 0.75rem;
  color: var(--text);
}

h1 {
  font-size: 2rem;
  margin-top: 0;
  padding-bottom: 0.5rem;
  border-bottom: 2px solid var(--accent);
}

h2 { font-size: 1.4rem; }
h3 { font-size: 1.15rem; margin-top: 1.5rem; }

p, li { margin-bottom: 0.75rem; color: var(--text); }

a {
  color: var(--accent);
  text-decoration: none;
}
a:hover { text-decoration: underline; }

code {
  font-family: 'JetBrains Mono', monospace;
  font-size: 0.875em;
  background: var(--code-bg);
  padding: 0.15em 0.4em;
  border-radius: 4px;
}

pre {
  font-family: 'JetBrains Mono', monospace;
  font-size: 14px;
  background: var(--code-bg);
  padding: 1rem;
  border-radius: 8px;
  overflow-x: auto;
  margin: 1rem 0;
  line-height: 1.5;
  border: 1px solid var(--border);
}

pre code {
  background: none;
  padding: 0;
  font-size: inherit;
}

blockquote {
  border-left: 4px solid var(--accent);
  padding: 0.75rem 1rem;
  margin: 1rem 0;
  background: var(--accent-light);
  border-radius: 0 8px 8px 0;
}

blockquote p { margin-bottom: 0; }

ul, ol { padding-left: 1.5rem; margin: 0.75rem 0; }

table {
  width: 100%;
  border-collapse: collapse;
  margin: 1rem 0;
  font-size: 0.9rem;
}

th, td {
  padding: 0.6rem 0.75rem;
  text-align: left;
  border-bottom: 1px solid var(--border);
}

th {
  background: var(--code-bg);
  font-weight: 600;
}

tr:last-child td { border-bottom: none; }

hr {
  border: none;
  border-top: 1px solid var(--hr);
  margin: 2rem 0;
}

.callout {
  padding: 1rem 1.25rem;
  border-radius: 8px;
  margin: 1rem 0;
  border-left: 4px solid var(--accent);
  background: var(--accent-light);
}

.callout-warn {
  border-color: var(--orange);
  background: var(--orange-bg);
}

.callout-tip {
  border-color: var(--green);
  background: var(--green-bg);
}

.callout-danger {
  border-color: var(--red);
  background: var(--red-bg);
}

.step {
  display: flex;
  gap: 1rem;
  margin: 1.25rem 0;
  padding: 0.75rem 1rem;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 8px;
}

.step-num {
  flex-shrink: 0;
  width: 28px;
  height: 28px;
  background: var(--accent);
  color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 14px;
  font-weight: 600;
}

.step-content { flex: 1; }
.step-content p:last-child { margin-bottom: 0; }

.quiz-item {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 8px;
  padding: 1rem 1.25rem;
  margin: 1rem 0;
}

.quiz-item p { margin-bottom: 0.5rem; font-weight: 500; }

.quiz-answer {
  display: none;
  margin-top: 0.5rem;
  padding: 0.75rem;
  background: var(--green-bg);
  border-radius: 6px;
  font-size: 0.9rem;
}

.quiz-answer.show { display: block; }

.quiz-toggle {
  background: var(--accent);
  color: white;
  border: none;
  padding: 0.4rem 0.8rem;
  border-radius: 6px;
  cursor: pointer;
  font-size: 0.8rem;
  font-family: inherit;
}

.quiz-toggle:hover { background: var(--accent-dark); }

.meta {
  font-size: 0.85rem;
  color: var(--text-secondary);
  margin-bottom: 1.5rem;
}

.meta span { margin-right: 1rem; }
```
