---
id: a1b2c3d4-1192-4000-8000-000000000192
title: Vue.js
language: markdown
tags: [web-dev, vue, frontend]
selection: null
isPinned: false
timestamp: 1781500001192
---

**Links**: [[React]] | [[Angular]] | [[Svelte]] | [[Next.js and Modern Web Frameworks]] | [[State Management Patterns]] | [[Vite and esbuild]]


# Vue.js

Vue.js is a progressive JavaScript framework for building user interfaces. It's designed to be incrementally adoptable.

## Core Features

| Feature | Description |
|---------|-------------|
| Reactive data binding | Automatic DOM updates when data changes |
| Component system | Reusable, encapsulated components |
| Virtual DOM | Efficient rendering |
| SFC (Single File Component) | Template + script + style in one .vue file |
| Composition API | Logic composition with setup() |

## Basic Component

```vue
<script setup>
import { ref, computed, onMounted } from 'vue'

const count = ref(0)
const name = ref('World')

const greeting = computed(() => `Hello, ${name.value}!`)

function increment() {
    count.value++
}

onMounted(() => {
    console.log('Component mounted')
})
</script>

<template>
    <div class="welcome">
        <h1>{{ greeting }}</h1>
        <p>Count: {{ count }}</p>
        <button @click="increment">+1</button>
        <input v-model="name" placeholder="Enter name" />
    </div>
</template>

<style scoped>
.welcome {
    padding: 20px;
    font-family: sans-serif;
}
</style>
```

## Reactivity

```javascript
import { ref, reactive, computed, watch } from 'vue'

// Reactive primitives
const count = ref(0)
const message = ref('hello')

// Reactive objects
const user = reactive({
    name: 'Alice',
    age: 30,
    address: { city: 'Portland' }
})

// Computed (cached, derived values)
const double = computed(() => count.value * 2)
const isAdult = computed(() => user.age >= 18)

// Watchers
watch(count, (newVal, oldVal) => {
    console.log(`Count changed: ${oldVal} → ${newVal}`)
})

watchEffect(() => {
    console.log(`Count is: ${count.value}`)  // Runs immediately and on change
})
```

## Directives

```vue
<template>
    <!-- Conditional rendering -->
    <div v-if="loading">Loading...</div>
    <div v-else-if="error">{{ error }}</div>
    <div v-else>Content loaded</div>

    <!-- List rendering -->
    <li v-for="(item, index) in items" :key="item.id">
        {{ index }}: {{ item.name }}
    </li>

    <!-- Event handling -->
    <button @click="handleClick">Click</button>
    <button @click.stop="handleClick">Stop propagation</button>
    <button @click.once="handleClick">Once</button>

    <!-- Class and style binding -->
    <div :class="{ active: isActive, disabled: !isEnabled }"
         :style="{ color: themeColor, fontSize: size + 'px' }">
    </div>

    <!-- v-model (two-way binding) -->
    <input v-model="username" />
    <input v-model.number="age" type="number" />
    <input v-model.trim="name" />
</template>
```

## Component Communication

```vue
<!-- Parent → Child: props -->
<!-- Child.vue -->
<script setup>
const props = defineProps({
    title: { type: String, required: true },
    count: { type: Number, default: 0 },
    items: { type: Array, default: () => [] }
})
</script>

<!-- Child → Parent: emits -->
<script setup>
const emit = defineEmits(['update', 'delete'])
function handleUpdate() {
    emit('update', { id: 1, value: 'new' })
}
</script>

<!-- Parent usage -->
<template>
    <ChildComponent
        title="My List"
        :count="items.length"
        @update="handleChildUpdate"
    />
</template>
```

## Composition API Patterns

```javascript
// Composable (reusable logic)
import { ref, onMounted, onUnmounted } from 'vue'

export function useMouse() {
    const x = ref(0)
    const y = ref(0)

    function update(event) {
        x.value = event.clientX
        y.value = event.clientY
    }

    onMounted(() => window.addEventListener('mousemove', update))
    onUnmounted(() => window.removeEventListener('mousemove', update))

    return { x, y }
}

// Usage in component
<script setup>
import { useMouse } from './composables/useMouse'
const { x, y } = useMouse()
</script>
<template>Mouse: {{ x }}, {{ y }}</template>
```

## Router

```javascript
import { createRouter, createWebHistory } from 'vue-router'

const routes = [
    { path: '/', component: Home },
    { path: '/users/:id', component: User, props: true },
    { path: '/admin', component: Admin, meta: { requiresAuth: true } },
    { path: '/:pathMatch(.*)*', component: NotFound }
]

const router = createRouter({
    history: createWebHistory(),
    routes
})

router.beforeEach((to, from) => {
    if (to.meta.requiresAuth && !isAuthenticated()) {
        return '/login'
    }
})
```

## Pinia (State Management)

```javascript
import { defineStore } from 'pinia'

export const useCartStore = defineStore('cart', {
    state: () => ({
        items: [],
        discount: 0
    }),
    getters: {
        total: (state) => state.items.reduce((sum, i) => sum + i.price * i.qty, 0),
        discountedTotal() {
            return this.total * (1 - this.discount)
        }
    },
    actions: {
        addItem(product) {
            const existing = this.items.find(i => i.id === product.id)
            if (existing) {
                existing.qty++
            } else {
                this.items.push({ ...product, qty: 1 })
            }
        },
        async checkout() {
            const response = await fetch('/api/orders', {
                method: 'POST',
                body: JSON.stringify({ items: this.items })
            })
            if (response.ok) this.items = []
        }
    }
})
```
