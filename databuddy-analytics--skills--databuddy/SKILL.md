---
name: databuddy
description: Privacy-first analytics SDK. Covers browser tracking, server-side events, feature flags, and a REST API. Use when this capability is needed.
metadata:
  author: databuddy-analytics
---
# Databuddy SDK (v2.4)

Privacy-first analytics SDK. Covers browser tracking, server-side events, feature flags, and a REST API.

## External Documentation

For the latest docs, fetch: **https://databuddy.cc/llms.txt**

## When to Use This Skill

- Setting up analytics in React/Next.js/Vue applications
- Implementing server-side event tracking in Node.js
- Adding feature flags (React, Vue, or Node.js)
- Tracking custom events, errors, or Web Vitals
- Designing event taxonomies, naming events, or choosing properties
- Querying analytics data via the REST API

## SDK Entry Points

| Import | Environment | Description |
|--------|-------------|-------------|
| `@databuddy/sdk` | Browser (Core) | Tracking utilities, types, script injection |
| `@databuddy/sdk/react` | React/Next.js | `<Databuddy />` component, flags hooks, core re-exports |
| `@databuddy/sdk/node` | Node.js/Server | `Databuddy` class (API-key auth), `ServerFlagsManager` |
| `@databuddy/sdk/vue` | Vue 3 | `<Databuddy />` component, flags plugin and composables |

> There is **no** `@databuddy/sdk/ai/vercel` entry point. AI/LLM tracking is not part of the SDK.

## Quick Start

### React / Next.js

```tsx
import { Databuddy } from "@databuddy/sdk/react";

// app/layout.tsx
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Databuddy trackWebVitals trackErrors />
      </body>
    </html>
  );
}
```

`clientId` is auto-detected from `NEXT_PUBLIC_DATABUDDY_CLIENT_ID`. Pass it explicitly if needed.

### Node.js (Server-Side)

```typescript
import { Databuddy } from "@databuddy/sdk/node";

const client = new Databuddy({
  apiKey: process.env.DATABUDDY_API_KEY!, // required, format: dbdy_xxx
});

await client.track({
  name: "api_call",
  properties: { endpoint: "/users", method: "GET" },
});

// Flush before process exit in serverless
await client.flush();
```

### Feature Flags (React)

```tsx
import { FlagsProvider, useFlag } from "@databuddy/sdk/react";

<FlagsProvider clientId="..." user={{ userId: "123" }}>
  <App />
</FlagsProvider>

function MyComponent() {
  const { on, loading } = useFlag("dark-mode");
  if (loading) return <Skeleton />;
  return on ? <DarkTheme /> : <LightTheme />;
}
```

### Custom Event Tracking (Browser)

```typescript
import { track } from "@databuddy/sdk/react";

track("purchase", { product_id: "sku-123", amount: 99.99 });
```

### Global Properties (Browser)

```typescript
window.databuddy?.setGlobalProperties({
  plan: "enterprise",
  abVariant: "checkout-v2",
});
```

## Key Browser Config (`DatabuddyConfig`)

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `clientId` | `string` | Auto-detect | Project client ID |
| `disabled` | `boolean` | `false` | Disable all tracking |
| `trackWebVitals` | `boolean` | `false` | Track Web Vitals metrics |
| `trackErrors` | `boolean` | `false` | Track JavaScript errors |
| `trackPerformance` | `boolean` | `true` | Track performance metrics |
| `trackInteractions` | `boolean` | `false` | Track user interactions |
| `trackOutgoingLinks` | `boolean` | `false` | Track outgoing link clicks |
| `trackHashChanges` | `boolean` | `false` | Track URL hash changes |
| `trackAttributes` | `boolean` | `false` | Track `data-*` attributes |
| `enableBatching` | `boolean` | `true` | Batch events before sending |
| `samplingRate` | `number` | `1.0` | Sampling rate (0.0-1.0) |
| `skipPatterns` | `string[]` | — | Glob patterns to skip tracking |
| `maskPatterns` | `string[]` | — | Glob patterns to mask paths |
| `debug` | `boolean` | `false` | Enable debug logging |

## Detailed Documentation

- [Core SDK](./databuddy-core/SKILL.md) — Browser tracking, config, events, declarative tracking
- [React](./databuddy-react/SKILL.md) — Component, hooks, re-exports
- [Node.js](./databuddy-node/SKILL.md) — Server-side tracking, batching, middleware, dedup
- [Feature Flags](./databuddy-flags/SKILL.md) — Flags for React, Vue, and Node.js
- [Vue](./databuddy-vue/SKILL.md) — Vue 3 component, plugin, composables
- [Event Design](./databuddy-events/SKILL.md) — Naming, properties, taxonomies, review checklist
- [REST API](./databuddy-api/SKILL.md) — Flags endpoints, event tracking API

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/databuddy-analytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
