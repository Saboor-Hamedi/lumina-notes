---
id: a1b2c3d4-1064-4000-8000-000000000064
title: Next.js and Modern Web Frameworks
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001064
---
# Next.js and Modern Web Frameworks

Next.js is a React meta-framework that adds server-side rendering, static generation, routing, and API capabilities. It represents the modern full-stack web framework pattern.

## Meta-Framework Landscape

| Framework | Base | Rendering | Best For |
|-----------|------|-----------|----------|
| Next.js | React | SSR + SSG + ISR | Full-stack React apps |
| Nuxt.js | Vue | SSR + SSG + ISR | Full-stack Vue apps |
| SvelteKit | Svelte | SSR + SSG | Lightweight apps |
| Remix | React | SSR (web standard) | Data-intensive apps |
| Astro | Multi-framework | Static-first | Content sites |

## Rendering Strategies

| Strategy | When | How |
|----------|------|-----|
| SSR (Server-Side Rendering) | Dynamic content per request | Render on server, hydrate on client |
| SSG (Static Site Generation) | Static content | Build-time render, serve as HTML |
| ISR (Incremental Static Regeneration) | Semi-dynamic, periodically updated | SSG + revalidate on demand |
| CSR (Client-Side Rendering) | Highly interactive dashboards | JS renders in browser |

## Next.js App Router

```tsx
// app/page.tsx — Home route
export default function Home() {
  return <h1>Hello World</h1>;
}

// app/users/[id]/page.tsx — Dynamic route
export default async function UserPage({ params }: { params: { id: string } }) {
  const user = await fetch(`https://api.example.com/users/${params.id}`).then(r => r.json());
  return <div>{user.name}</div>;
}

// app/api/users/route.ts — API route
export async function GET() {
  const users = await db.query("SELECT * FROM users");
  return Response.json(users);
}
```

## Server Components vs Client Components

| Aspect | Server Component | Client Component |
|--------|-----------------|-----------------|
| Rendering | Server-only | Browser |
| Bundle size | Zero JS | Full JS included |
| Data access | Direct (DB, filesystem) | API calls |
| Interactivity | No | Yes (useState, useEffect) |
| Default in App Router | Yes | Opt-in with `"use client"` |

## Data Fetching Patterns

```tsx
// Server Component — fetch directly
async function Posts() {
  const posts = await db.query("SELECT * FROM posts");
  return posts.map(p => <Post key={p.id} post={p} />);
}

// Parallel data fetching
async function Dashboard({ userId }: { userId: string }) {
  const [user, posts, analytics] = await Promise.all([
    getUser(userId),
    getPosts(userId),
    getAnalytics(userId),
  ]);
  return <Layout user={user} posts={posts} analytics={analytics} />;
}
```

## Caching and Revalidation

```tsx
// ISR — revalidate every 60 seconds
export const revalidate = 60;

// On-demand revalidation
// POST /api/revalidate?secret=token
export async function POST(request: Request) {
  const body = await request.json();
  revalidatePath(`/posts/${body.slug}`);
  return Response.json({ revalidated: true });
}
```

## Deployment

| Platform | Features |
|----------|----------|
| Vercel | Zero-config, edge functions, ISR |
| Netlify | Serverless + SSR support |
| AWS (Amplify, Lambda) | Full control, custom infra |
| Docker | Self-hosted, portable |

**Links**: [[React]] | [[Web Development Fundamentals]] | [[TypeScript]] | [[REST API Design]] | [[Docker Containers]]
