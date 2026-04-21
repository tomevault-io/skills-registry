---
name: databuddy-flags
description: Feature flags across React, Vue, and Node.js. All share the same `FlagsConfig`, `FlagResult`, and `FlagState` types. Use when this capability is needed.
metadata:
  author: databuddy-analytics
---
# Feature Flags

Feature flags across React, Vue, and Node.js. All share the same `FlagsConfig`, `FlagResult`, and `FlagState` types.

## Shared Types

```typescript
interface FlagsConfig {
  clientId: string;             // Required
  apiUrl?: string;              // Default: "https://api.databuddy.cc"
  autoFetch?: boolean;          // Default: true (browser), false (server)
  cacheTtl?: number;            // Default: 60000ms
  staleTime?: number;           // Default: cacheTtl / 2
  disabled?: boolean;
  isPending?: boolean;          // Defer evaluation until session resolves
  skipStorage?: boolean;        // Skip localStorage (browser only)
  debug?: boolean;
  environment?: string;         // Target specific environment
  defaults?: Record<string, boolean | string | number>;
  user?: UserContext;
}

interface UserContext {
  userId?: string;
  email?: string;
  organizationId?: string;
  teamId?: string;
  properties?: Record<string, unknown>;
}

interface FlagResult {
  enabled: boolean;
  value: boolean | string | number;
  variant?: string;
  payload: Record<string, unknown> | null;
  reason: string;
}

type FlagStatus = "loading" | "ready" | "error" | "pending";

interface FlagState {
  on: boolean;
  loading: boolean;
  status: FlagStatus;
  value?: boolean | string | number;
  variant?: string;
}
```

**Reason codes:** `"DEFAULT"`, `"USER_RULE_MATCH"`, `"TARGET_GROUP_MATCH"`, `"ROLLOUT_ENABLED"`, `"ROLLOUT_DISABLED"`, `"MULTIVARIANT_EVALUATED"`, `"BOOLEAN_DEFAULT"`, `"ERROR"`, `"SESSION_PENDING"`, `"FLAG_NOT_FOUND"`, `"NO_PROVIDER"`

---

## React (`@databuddy/sdk/react`)

### FlagsProvider

Wrap your app to enable flag hooks:

```tsx
import { FlagsProvider } from "@databuddy/sdk/react";

<FlagsProvider
  clientId="your-client-id"
  user={{ userId: "user-123", email: "user@example.com" }}
  environment="production"
>
  <App />
</FlagsProvider>
```

Props extend `FlagsConfig` plus `children: ReactNode`.

### useFlag(key)

Returns a `FlagState` for a single flag. Triggers background fetch if not cached.

```tsx
import { useFlag } from "@databuddy/sdk/react";

function MyComponent() {
  const { on, loading, value, variant } = useFlag("new-checkout");
  if (loading) return <Skeleton />;
  return on ? <NewCheckout /> : <OldCheckout />;
}
```

### useFlags()

Returns the full `FlagsContext` with imperative methods:

```typescript
interface FlagsContext {
  getFlag(key: string): FlagState;
  isOn(key: string): boolean;
  getValue<T>(key: string, defaultValue?: T): T;
  fetchFlag(key: string): Promise<FlagResult>;
  fetchAllFlags(): Promise<void>;
  updateUser(user: UserContext): void;
  refresh(forceClear?: boolean): Promise<void>;
  isReady: boolean;
}
```

```tsx
const { isOn, getValue, updateUser } = useFlags();

if (isOn("premium-features")) {
  const tier = getValue("pricing-tier", "free");
}

// After login
updateUser({ userId: "user-123", email: "user@example.com" });
```

Falls back to safe defaults if used outside `FlagsProvider` (with a console warning).

---

## Vue (`@databuddy/sdk/vue`)

### Plugin Setup

```typescript
import { createFlagsPlugin } from "@databuddy/sdk/vue";

const app = createApp(App);
app.use(createFlagsPlugin({
  clientId: "your-client-id",
  user: { userId: "user-123" },
}));
```

### useFlag(key)

Returns reactive computed refs:

```vue
<script setup>
import { useFlag } from "@databuddy/sdk/vue";

const { on, loading, state } = useFlag("new-feature");
</script>

<template>
  <Skeleton v-if="loading" />
  <NewFeature v-else-if="on" />
  <OldFeature v-else />
</template>
```

- `on: ComputedRef<boolean>`
- `loading: ComputedRef<boolean>`
- `state: ComputedRef<FlagState>`

### useFlags()

Returns imperative methods (not reactive). Throws if plugin not installed.

```typescript
const { getFlag, fetchAllFlags, updateUser, refresh, updateConfig, memoryFlags } = useFlags();
```

---

## Node.js (`@databuddy/sdk/node`)

### createServerFlagsManager

```typescript
import { createServerFlagsManager } from "@databuddy/sdk/node";

const flags = createServerFlagsManager({
  clientId: process.env.DATABUDDY_CLIENT_ID!,
  autoFetch: true,
});

await flags.waitForInit();
```

### ServerFlagsManager Methods

| Method | Signature | Description |
|--------|-----------|-------------|
| `waitForInit` | `() => Promise<void>` | Wait for initial fetch to complete |
| `getFlag` | `(key: string, user?: UserContext) => Promise<FlagResult>` | Evaluate a single flag |
| `fetchAllFlags` | `(user?: UserContext) => Promise<void>` | Fetch all flags |
| `isEnabled` | `(key: string) => FlagState` | Synchronous check (uses cache) |
| `getValue` | `<T>(key: string, defaultValue?: T) => T` | Get flag value with default |
| `updateUser` | `(user: UserContext) => void` | Update user context and refresh |
| `refresh` | `(forceClear?: boolean) => Promise<void>` | Re-fetch all flags |
| `getMemoryFlags` | `() => Record<string, FlagResult>` | Get all cached flags |

Differences from browser:
- `autoFetch` defaults to `false`
- No localStorage persistence (`skipStorage: true`)
- Batch delay is 5ms (vs 10ms in browser)

---

## Caching Strategy

- **LRU cache** with TTL (`cacheTtl`, default 60s)
- **Stale-while-revalidate**: after `staleTime` (default 30s), serves cached result while fetching fresh data in background
- **Browser persistence**: localStorage with key `db-flags` (disable with `skipStorage: true`)
- **Request batching**: individual `getFlag` calls within the batch delay window (10ms browser, 5ms server) are combined into a single bulk API request

## Auto-Tracking (Browser Only)

When a flag is evaluated in the browser, a `$flag_evaluated` event is automatically tracked (deduplicated by key+value):

```typescript
// Automatically sent:
{ name: "$flag_evaluated", properties: { flag: "new-checkout", value: true, variant: "v2", enabled: true } }
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/databuddy-analytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
