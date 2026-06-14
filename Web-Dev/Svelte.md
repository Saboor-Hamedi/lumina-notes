---
id: a1b2c3d4-1193-4000-8000-000000000193
title: Svelte
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001193
---
# Svelte

Svelte is a compiler-based frontend framework that shifts work from runtime to compile time, producing highly optimized vanilla JavaScript.

## Key Difference

```
React/Vue: Virtual DOM diffing at runtime
  Component → Virtual DOM → Diff → Real DOM

Svelte: Compiles to direct DOM operations
  Component → Compiler → Direct DOM manipulation code
  (No virtual DOM, no runtime framework)
```

## Basic Component

```svelte
<script>
    let count = 0;
    let name = 'World';
    $: greeting = `Hello, ${name}!`;
    $: doubled = count * 2;

    function increment() {
        count += 1;
    }

    // Reactive statements with dependencies
    $: if (count > 10) {
        console.log('Count is high!');
    }
</script>

<div class="welcome">
    <h1>{greeting}</h1>
    <p>Count: {count} (doubled: {doubled})</p>
    <button on:click={increment}>+1</button>
    <input bind:value={name} placeholder="Enter name" />
</div>

<style>
    .welcome {
        padding: 20px;
        font-family: sans-serif;
    }
    h1 {
        color: #ff3e00;
    }
</style>
```

## Reactivity

```svelte
<script>
    // Reactive declarations
    let a = 1;
    let b = 2;
    $: sum = a + b;                  // Recalculated when a or b changes
    $: difference = a - b;
    $: {
        console.log(`sum = ${sum}`); // Block statements
        console.log(`diff = ${difference}`);
    }

    // Reactive assignments
    let items = [];
    function addItem(item) {
        items = [...items, item];     // Must reassign for reactivity
    }

    // Svelte tracks assignments, not mutations
    // Use `items = items` after mutation to trigger reactivity
</script>

<p>Sum: {sum}</p>
```

## Props and Events

```svelte
<!-- Child.svelte -->
<script>
    export let title;                 // Prop declaration
    export let count = 0;             // With default
    export let items = [];            // Default array

    import { createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher();

    function handleClick() {
        dispatch('update', { id: 1, value: 'new' });
    }

    function handleDelete() {
        dispatch('delete', 1);
    }
</script>

<button on:click={handleClick}>Update</button>
<slot />                             <!-- Slot for children -->

<!-- Parent.svelte -->
<script>
    import Child from './Child.svelte';

    function handleUpdate(event) {
        console.log(event.detail);    // { id: 1, value: 'new' }
    }
</script>

<Child title="Hello" on:update={handleUpdate}>
    <p>This is slot content</p>
</Child>
```

## Stores

```svelte
// stores.js
import { writable, derived, readable } from 'svelte/store';

export const count = writable(0);
export const user = writable(null);
export const items = writable([]);

export const doubled = derived(count, ($count) => $count * 2);

export const time = readable(new Date(), (set) => {
    const interval = setInterval(() => set(new Date()), 1000);
    return () => clearInterval(interval);
});

// Custom store
function createTodos() {
    const { subscribe, update, set } = writable([]);

    return {
        subscribe,
        add: (text) => update(todos => [...todos, { id: Date.now(), text, done: false }]),
        toggle: (id) => update(todos => todos.map(t => t.id === id ? { ...t, done: !t.done } : t)),
        clear: () => set([]),
    };
}

export const todos = createTodos();
```

```svelte
<!-- Usage in components -->
<script>
    import { count, doubled, todos } from './stores.js';
    import { onDestroy } from 'svelte';
    import { count } from './stores.js';

    // Auto-subscribe (with $ prefix)
    // $count, $doubled, $todos

    // Manual subscribe/unsubscribe
    const unsub = count.subscribe(val => console.log(val));
    onDestroy(unsub);
</script>

<p>Count: {$count}</p>
<p>Doubled: {$doubled}</p>
```

## Lifecycle

```svelte
<script>
    import { onMount, beforeUpdate, afterUpdate, onDestroy } from 'svelte';

    let canvas;

    onMount(() => {
        const ctx = canvas.getContext('2d');
        // Setup canvas...
        return () => {
            // Cleanup (called on destroy)
        };
    });

    beforeUpdate(() => {
        console.log('Before DOM update');
    });

    afterUpdate(() => {
        console.log('After DOM update');
    });

    onDestroy(() => {
        console.log('Component destroyed');
    });
</script>

<canvas bind:this={canvas} />
```

## Transitions and Animations

```svelte
<script>
    import { fade, fly, slide, scale } from 'svelte/transition';
    import { crossfade, flip } from 'svelte/animation';
    import { spring, tweened } from 'svelte/motion';

    let visible = false;
    let items = [1, 2, 3];

    const size = spring(10, { stiffness: 0.5, damping: 0.3 });
    const progress = tweened(0, { duration: 1000 });
</script>

<button on:click={() => visible = !visible}>Toggle</button>

{#if visible}
    <div transition:fade={{ duration: 300 }}>Fade</div>
    <div transition:fly={{ x: 200, duration: 500 }}>Fly</div>
    <div transition:slide>Slide</div>
    <div in:scale out:scale={{ start: 0.5 }}>Scale</div>
{/if}

{#each items as item (item.id)}
    <div animate:flip>{item.name}</div>
{/each}
```

## SvelteKit (Full-Stack Framework)

```svelte
<!-- src/routes/+page.svelte -->
<script>
    export let data;
    const { posts } = data;
</script>

{#each posts as post}
    <article>
        <h2><a href="/blog/{post.slug}">{post.title}</a></h2>
    </article>
{/each}
```

```javascript
// src/routes/+page.server.js
export async function load({ fetch }) {
    const response = await fetch('/api/posts');
    const posts = await response.json();
    return { posts };
}

// src/routes/api/posts/+server.js
export async function GET() {
    const posts = await db.query('SELECT * FROM posts ORDER BY created_at DESC');
    return json(posts);
}

export async function POST({ request }) {
    const data = await request.json();
    const post = await db.query('INSERT INTO posts (title, content) VALUES ($1, $2) RETURNING *',
        [data.title, data.content]);
    return json(post, { status: 201 });
}
```
