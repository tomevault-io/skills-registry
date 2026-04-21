---
name: pwa-offline-patterns
description: Progressive Web App patterns for offline-first React applications using Workbox, IndexedDB, and Web Push. This skill should be used when implementing PWA features, service workers, offline functionality, push notifications, or caching strategies. Triggers on tasks involving PWA, service worker, offline, Web Push, or caching. Use when this capability is needed.
metadata:
  author: tombensim
---

# PWA & Offline Patterns

Best practices for building offline-first Progressive Web Apps with React, Vite, Workbox, and Web Push.

## When to Apply

Reference these guidelines when:

- Configuring vite-plugin-pwa
- Implementing offline data access with IndexedDB
- Building an offline mutation queue
- Setting up Web Push notifications
- Writing service worker caching strategies

## Rule Categories

| Category           | Impact   | Prefix     |
| ------------------ | -------- | ---------- |
| Service Worker     | CRITICAL | `sw-`      |
| Offline Data       | HIGH     | `offline-` |
| Push Notifications | HIGH     | `push-`    |
| Caching Strategy   | MEDIUM   | `cache-`   |

## Rules

### Service Worker (CRITICAL)

#### sw-vite-plugin

Use vite-plugin-pwa with generateSW mode for automatic service worker generation.

```typescript
// vite.config.ts
import { VitePWA } from 'vite-plugin-pwa';

export default defineConfig({
  plugins: [
    react(),
    VitePWA({
      registerType: 'autoUpdate',
      includeAssets: ['favicon.ico', 'apple-touch-icon.png'],
      manifest: {
        name: 'Tennis Group Manager',
        short_name: 'Tennis',
        theme_color: '#16a34a',
        background_color: '#ffffff',
        display: 'standalone',
        start_url: '/',
        icons: [
          { src: '/pwa-192.png', sizes: '192x192', type: 'image/png' },
          { src: '/pwa-512.png', sizes: '512x512', type: 'image/png' },
          { src: '/pwa-512.png', sizes: '512x512', type: 'image/png', purpose: 'maskable' },
        ],
      },
      workbox: {
        globPatterns: ['**/*.{js,css,html,ico,png,svg,woff2}'],
        runtimeCaching: [
          {
            urlPattern: /^https:\/\/api\..*\/v1\//,
            handler: 'NetworkFirst',
            options: {
              cacheName: 'api-cache',
              expiration: { maxEntries: 100, maxAgeSeconds: 300 },
              networkTimeoutSeconds: 3,
            },
          },
        ],
      },
    }),
  ],
});
```

### Offline Data (HIGH)

#### offline-idb-store

Use `idb` library for typed IndexedDB access. Cache API responses for offline reads.

```typescript
import { openDB, DBSchema } from 'idb';

interface TennisDB extends DBSchema {
  events: { key: string; value: Event; indexes: { 'by-date': string } };
  pendingMutations: { key: number; value: PendingMutation; autoIncrement: true };
}

const dbPromise = openDB<TennisDB>('tennis-app', 1, {
  upgrade(db) {
    const eventStore = db.createObjectStore('events', { keyPath: 'id' });
    eventStore.createIndex('by-date', 'startTime');
    db.createObjectStore('pendingMutations', { autoIncrement: true });
  },
});

export async function cacheEvents(events: Event[]) {
  const db = await dbPromise;
  const tx = db.transaction('events', 'readwrite');
  await Promise.all(events.map((e) => tx.store.put(e)));
  await tx.done;
}

export async function getCachedEvents(): Promise<Event[]> {
  const db = await dbPromise;
  return db.getAllFromIndex('events', 'by-date');
}
```

#### offline-mutation-queue

Queue mutations when offline and drain on reconnection.

```typescript
interface PendingMutation {
  url: string;
  method: 'POST' | 'PUT' | 'DELETE';
  body: unknown;
  createdAt: number;
}

export async function queueMutation(mutation: Omit<PendingMutation, 'createdAt'>) {
  const db = await dbPromise;
  await db.add('pendingMutations', { ...mutation, createdAt: Date.now() });
}

export async function drainMutationQueue() {
  const db = await dbPromise;
  const pending = await db.getAll('pendingMutations');

  for (const mutation of pending) {
    try {
      await fetch(mutation.url, {
        method: mutation.method,
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(mutation.body),
      });
      await db.delete('pendingMutations', mutation.id!);
    } catch {
      break; // Stop on first failure, retry later
    }
  }
}

// Listen for connectivity changes
window.addEventListener('online', drainMutationQueue);
```

### Push Notifications (HIGH)

#### push-web-push-api

Use the Web Push API with VAPID keys. Store subscriptions server-side.

**Frontend subscription:**

```typescript
async function subscribeToPush() {
  const reg = await navigator.serviceWorker.ready;
  const subscription = await reg.pushManager.subscribe({
    userVisibleOnly: true,
    applicationServerKey: urlBase64ToUint8Array(VAPID_PUBLIC_KEY),
  });

  await api.post('/notifications/push/subscribe', {
    endpoint: subscription.endpoint,
    keys: {
      p256dh: arrayBufferToBase64(subscription.getKey('p256dh')!),
      auth: arrayBufferToBase64(subscription.getKey('auth')!),
    },
  });
}
```

**Backend sending:**

```typescript
import webpush from 'web-push';

webpush.setVapidDetails(
  'mailto:admin@tennisgroup.com',
  process.env.VAPID_PUBLIC_KEY!,
  process.env.VAPID_PRIVATE_KEY!,
);

async function sendPush(userId: string, payload: NotificationPayload) {
  const { rows: subs } = await pool.query(
    'SELECT endpoint, p256dh_key, auth_key FROM push_subscriptions WHERE user_id = $1',
    [userId],
  );

  for (const sub of subs) {
    try {
      await webpush.sendNotification(
        { endpoint: sub.endpoint, keys: { p256dh: sub.p256dh_key, auth: sub.auth_key } },
        JSON.stringify(payload),
      );
    } catch (err: any) {
      if (err.statusCode === 410) {
        await pool.query('DELETE FROM push_subscriptions WHERE endpoint = $1', [sub.endpoint]);
      }
    }
  }
}
```

### Caching Strategy (MEDIUM)

#### cache-strategy-selection

Choose caching strategy based on content type:

| Content                  | Strategy                 | Reason                                |
| ------------------------ | ------------------------ | ------------------------------------- |
| App shell (HTML/JS/CSS)  | **CacheFirst**           | Versioned by Vite hashes              |
| API data (events, users) | **NetworkFirst**         | Fresh data preferred, cached fallback |
| Static assets (images)   | **CacheFirst**           | Rarely changes                        |
| User avatar uploads      | **StaleWhileRevalidate** | Show cached, update in background     |

#### cache-tanstack-query-offline

Configure TanStack Query for offline persistence.

```typescript
import { QueryClient } from '@tanstack/react-query';
import { createSyncStoragePersister } from '@tanstack/query-sync-storage-persister';
import { persistQueryClient } from '@tanstack/react-query-persist-client';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60 * 5, // 5 minutes
      gcTime: 1000 * 60 * 60 * 24, // 24 hours
      networkMode: 'offlineFirst',
    },
    mutations: {
      networkMode: 'offlineFirst',
    },
  },
});

const persister = createSyncStoragePersister({
  storage: window.localStorage,
});

persistQueryClient({
  queryClient,
  persister,
  maxAge: 1000 * 60 * 60 * 24, // 24 hours
});
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tombensim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
