---
name: databuddy-react
description: Drop-in component for React/Next.js and feature flag hooks. Use when this capability is needed.
metadata:
  author: databuddy-analytics
---
# React SDK (`@databuddy/sdk/react`)

Drop-in component for React/Next.js and feature flag hooks.

## Exports

**Component:**
- `Databuddy` — Injects the tracker script into `<head>`. Renders nothing.

**Flags (see [flags skill](../databuddy-flags/SKILL.md) for full details):**
- `FlagsProvider` — Context provider for feature flags
- `useFlag(key: string)` — Get a single flag's state
- `useFlags()` — Access the full flags context

**Core re-exports** (from `@databuddy/sdk`):
- `track`, `trackError`, `clear`, `flush`
- `getAnonymousId`, `getSessionId`, `getTrackingIds`, `getTrackingParams`
- `isTrackerAvailable`, `getTracker`

## `<Databuddy />` Component

Accepts all `DatabuddyConfig` props. Auto-detects `clientId` from `NEXT_PUBLIC_DATABUDDY_CLIENT_ID`.

### Next.js App Router

```tsx
// app/layout.tsx
import { Databuddy } from "@databuddy/sdk/react";

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

### Next.js Pages Router

```tsx
// pages/_app.tsx
import { Databuddy } from "@databuddy/sdk/react";

export default function App({ Component, pageProps }) {
  return (
    <>
      <Component {...pageProps} />
      <Databuddy trackWebVitals trackErrors />
    </>
  );
}
```

### Disable in Development

```tsx
<Databuddy disabled={process.env.NODE_ENV === "development"} trackWebVitals />
```

### All Features Enabled

```tsx
<Databuddy
  trackWebVitals
  trackErrors
  trackInteractions
  trackOutgoingLinks
  trackHashChanges
  trackAttributes
  samplingRate={0.5}
/>
```

## Custom Event Tracking

```tsx
import { track, trackError } from "@databuddy/sdk/react";

function CheckoutButton() {
  return (
    <button onClick={() => track("checkout_clicked", { cartSize: 3 })}>
      Checkout
    </button>
  );
}

// Error tracking
try {
  await riskyOperation();
} catch (error) {
  trackError(error.message, {
    stack: error.stack,
    error_type: error.name,
  });
}
```

## SSR Safety

All functions are safe to call during SSR — they no-op when `window` is undefined. The `<Databuddy />` component only injects the script on the client.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/databuddy-analytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
