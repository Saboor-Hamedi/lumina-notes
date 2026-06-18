---
id: new-web-032-0000-0000-0000-000000000032
title: Progressive Web Apps (PWAs)
language: markdown
tags:
  - web-dev
  - frontend
  - mobile
selection: null
isPinned: false
timestamp: 1781769201734
---

# Progressive Web Apps (PWAs)

**Links**: [[Web Development Fundamentals]] | [[React]] | [[Vite and esbuild]] | [[Web Accessibility]] | [[HTTP Caching]] | [[Web Security]]

**See also**: [[React Native]], [[Service Workers]], [[Web APIs]], [[Desktop Apps with Electron and Tauri]]

## Overview

Progressive Web Apps combine the reach of the web with the capabilities of native apps: offline support, push notifications, home screen installation, and access to device APIs.

## Core Requirements

| Requirement | What It Does | Technology |
|-------------|--------------|------------|
| **HTTPS** | Secure communication | TLS certificate |
| **Service Worker** | Offline + caching | `service-worker.js` |
| **Web App Manifest** | Installable, splash screen, icons | `manifest.json` |
| **Responsive** | Works on any screen size | CSS media queries |

## Service Worker Lifecycle


```
Registered → Installing → Installed → Activating → Activated
                                                  ↓
                                             Fetch/Message Events
```

```javascript
// service-worker.js
const CACHE_NAME = "my-app-v1";
const ASSETS = ["/", "/index.html", "/styles.css", "/app.js"];

// Install: pre-cache core assets
self.addEventListener("install", (event) => {
    event.waitUntil(
        caches.open(CACHE_NAME).then((cache) => cache.addAll(ASSETS))
    );
});

// Activate: clean old caches
self.addEventListener("activate", (event) => {
    event.waitUntil(
        caches.keys().then((keys) => Promise.all(
            keys.filter((k) => k !== CACHE_NAME).map((k) => caches.delete(k))
        ))
    );
});

// Fetch: serve from cache, fallback to network
self.addEventListener("fetch", (event) => {
    event.respondWith(
        caches.match(event.request).then((cached) => {
            return cached || fetch(event.request).then((response) => {
                return caches.open(CACHE_NAME).then((cache) => {
                    cache.put(event.request, response.clone());
                    return response;
                });
            });
        })
    );
});
```

## Web App Manifest

```json
{
    "name": "My PWA",
    "short_name": "PWA",
    "description": "A progressive web application",
    "start_url": "/",
    "display": "standalone",
    "background_color": "#ffffff",
    "theme_color": "#3b82f6",
    "icons": [
        { "src": "/icon-192.png", "sizes": "192x192", "type": "image/png" },
        { "src": "/icon-512.png", "sizes": "512x512", "type": "image/png", "purpose": "maskable" }
    ]
}
```

## Caching Strategies

| Strategy | Behavior | Best For |
|----------|----------|----------|
| **Cache First** | Serve from cache, update from network | Static assets (images, CSS) |
| **Network First** | Try network, fallback to cache | API responses |
| **Stale While Revalidate** | Serve cached, update in background | Content that changes slowly |
| **Network Only** | Always fetch | Real-time data |
| **Cache Only** | Never fetch | Offline-first apps |

## Push Notifications

```javascript
// Register for push
async function subscribeToPush() {
    const registration = await navigator.serviceWorker.ready;
    const subscription = await registration.pushManager.subscribe({
        userVisibleOnly: true,
        applicationServerKey: publicVapidKey,
    });
    await fetch("/api/subscribe", {
        method: "POST",
        body: JSON.stringify(subscription),
    });
}

// Service worker: handle push
self.addEventListener("push", (event) => {
    const data = event.data.json();
    event.waitUntil(
        self.registration.showNotification(data.title, {
            body: data.body,
            icon: "/icon-192.png",
            badge: "/badge-72.png",
            data: { url: data.url },
        })
    );
});

// Handle notification click
self.addEventListener("notificationclick", (event) => {
    event.notification.close();
    clients.openWindow(event.notification.data.url);
});
```

## Background Sync

```javascript
// Register sync for offline form submission
async function submitOffline(formData) {
    const registration = await navigator.serviceWorker.ready;
    await registration.sync.register("submit-form");
    // Store form data in IndexedDB
    await idb.save("pending-forms", formData);
}

// Service worker: handle sync event
self.addEventListener("sync", (event) => {
    if (event.tag === "submit-form") {
        event.waitUntil(syncPendingForms());
    }
});
```

## Cross-Domain Connections

- [[React]] — create-react-app includes PWA support
- [[Vite and esbuild]] — PWA plugin for asset caching
- [[HTTP Caching]] — service worker caching builds on HTTP cache
- [[Web Security]] — HTTPS requirement, content security policies
- [[Desktop Apps with Electron and Tauri]] — alternative to native-like experiences
- [[React Native]] — full native vs PWA comparison
