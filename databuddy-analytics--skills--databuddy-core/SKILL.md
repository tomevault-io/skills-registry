---
name: databuddy-core
description: Browser-side tracking utilities. Inject the script, track events, and read IDs. Use when this capability is needed.
metadata:
  author: databuddy-analytics
---
# Core SDK (`@databuddy/sdk`)

Browser-side tracking utilities. Inject the script, track events, and read IDs.

## Installation

```bash
bun add @databuddy/sdk
```

## Exported Functions

| Function | Signature | Description |
|----------|-----------|-------------|
| `track` | `(name: string, properties?: Record<string, unknown>) => void` | Track a custom event. No-op on server. |
| `trackError` | `(message: string, properties?: { filename?, lineno?, colno?, stack?, error_type?, ... }) => void` | Convenience wrapper: calls `track("error", ...)` |
| `clear` | `() => void` | Reset session. Generates new anonymous/session IDs. Call after logout. |
| `flush` | `() => void` | Force-send all queued events immediately. |
| `isTrackerAvailable` | `() => boolean` | Check if tracker script has loaded. |
| `getTracker` | `() => DatabuddyTracker \| null` | Get the raw tracker instance. |
| `getAnonymousId` | `(urlParams?: URLSearchParams) => string \| null` | Get anonymous ID. Priority: URL params > localStorage (`did` key). |
| `getSessionId` | `(urlParams?: URLSearchParams) => string \| null` | Get session ID. Priority: URL params > sessionStorage (`did_session` key). |
| `getTrackingIds` | `(urlParams?: URLSearchParams) => { anonId, sessionId }` | Both IDs in one call. |
| `getTrackingParams` | `(urlParams?: URLSearchParams) => string` | IDs as query string: `"anonId=xxx&sessionId=yyy"`. For cross-domain links. |
| `detectClientId` | `(explicit?: string) => string \| undefined` | Resolve client ID from arg or env vars. |

## DatabuddyConfig (Browser)

```typescript
interface DatabuddyConfig {
  clientId?: string;           // Auto-detected from NEXT_PUBLIC_DATABUDDY_CLIENT_ID
  apiUrl?: string;             // Default: "https://basket.databuddy.cc"
  scriptUrl?: string;          // Default: "https://cdn.databuddy.cc/databuddy.js"
  disabled?: boolean;          // Default: false
  debug?: boolean;             // Default: false

  // Tracking features
  trackPerformance?: boolean;  // Default: true
  trackWebVitals?: boolean;    // Default: false
  trackErrors?: boolean;       // Default: false
  trackInteractions?: boolean; // Default: false
  trackOutgoingLinks?: boolean;// Default: false
  trackHashChanges?: boolean;  // Default: false
  trackAttributes?: boolean;   // Default: false (data-* attributes)

  // Optimization
  samplingRate?: number;       // 0.0-1.0, default: 1.0
  enableBatching?: boolean;    // Default: true
  batchSize?: number;          // Default: 10, max: 50
  batchTimeout?: number;       // Default: 2000ms (100-30000)
  enableRetries?: boolean;     // Default: true
  maxRetries?: number;         // Default: 3
  initialRetryDelay?: number;  // Default: 500ms

  // Filtering
  skipPatterns?: string[];     // Glob patterns to skip tracking
  maskPatterns?: string[];     // Glob patterns to mask paths
  filter?: (event: any) => boolean;

  // Advanced
  ignoreBotDetection?: boolean;// Default: false
  usePixel?: boolean;          // Use 1x1 pixel instead of script
  sdk?: string;                // Default: "web"
  sdkVersion?: string;         // Auto-set from package version
  clientSecret?: string;       // Server-side only
}
```

## Global Tracker (`window.databuddy` / `window.db`)

The tracker script exposes a global interface:

```typescript
interface DatabuddyTracker {
  track(eventName: string, properties?: Record<string, unknown>): void;
  screenView(properties?: Record<string, unknown>): void;
  setGlobalProperties(properties: Record<string, unknown>): void;
  clear(): void;
  flush(): void;
  options: DatabuddyConfig;
}
```

`window.db` is a shorthand with the same methods: `track`, `screenView`, `clear`, `flush`, `setGlobalProperties`.

## Pre-defined Event Types

| Event | Key Properties |
|-------|---------------|
| `screen_view` | `page_count`, `time_on_page`, `scroll_depth`, `interaction_count` |
| `page_exit` | `time_on_page`, `scroll_depth`, `interaction_count`, `page_count` |
| `button_click` | `button_text`, `button_type`, `button_id`, `element_class` |
| `form_submit` | `form_id`, `form_name`, `form_type`, `success` |
| `link_out` | `href`, `text`, `target_domain` |
| `web_vitals` | `fcp`, `lcp`, `cls`, `fid`, `ttfb`, `load_time`, `dom_ready_time`, `render_time`, `request_time` |
| `error` | `message`, `filename`, `lineno`, `colno`, `stack`, `error_type` |

Base properties attached to all events: `__path`, `__referrer`, `__title`, `__timestamp_ms`, `language`, `timezone`, `viewport_size`, `sessionId`, `page_count`, `utm_*`.

## Declarative Tracking (HTML data attributes)

```html
<button data-track="cta_clicked" data-button-text="Get Started" data-location="hero">
  Get Started
</button>

<a href="/pricing" data-track="nav_link_clicked" data-destination="pricing">
  Pricing
</a>
```

Data attributes are auto-converted from kebab-case to camelCase: `data-button-text` becomes `{ buttonText: "Get Started" }`.

Requires `trackAttributes: true` in config.

## Cross-Domain Tracking

```typescript
import { getTrackingParams } from "@databuddy/sdk";

// Append to outgoing URLs
const url = `https://app.example.com?${getTrackingParams()}`;

// On the receiving page, pass URL params to get the same IDs
const params = new URLSearchParams(window.location.search);
const { anonId, sessionId } = getTrackingIds(params);
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/databuddy-analytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
