---
name: databuddy-node
description: Server-side event tracking with batching, middleware, and deduplication. Also exports `ServerFlagsManager` for server-side feature flags. Use when this capability is needed.
metadata:
  author: databuddy-analytics
---
# Node.js SDK (`@databuddy/sdk/node`)

Server-side event tracking with batching, middleware, and deduplication. Also exports `ServerFlagsManager` for server-side feature flags.

## Exports

- `Databuddy` — Main event tracking class (also aliased as `db`)
- `ServerFlagsManager` — Server-side flags manager class
- `createServerFlagsManager(config)` — Factory function for `ServerFlagsManager`

## Quick Start

```typescript
import { Databuddy } from "@databuddy/sdk/node";

const client = new Databuddy({
  apiKey: process.env.DATABUDDY_API_KEY!, // required, format: dbdy_xxx
});

await client.track({
  name: "user_signup",
  properties: { plan: "pro", source: "web" },
});

// Flush before exit in serverless
await client.flush();
```

## Constructor Config

```typescript
interface DatabuddyConfig {
  apiKey: string;                       // Required. Format: dbdy_xxx
  apiUrl?: string;                      // Default: "https://basket.databuddy.cc"
  websiteId?: string;                   // Default website scope for events
  namespace?: string;                   // Default namespace (e.g., "billing", "auth")
  source?: string;                      // Default source (e.g., "backend", "webhook")

  // Batching
  enableBatching?: boolean;             // Default: true
  batchSize?: number;                   // Default: 10, max: 100
  batchTimeout?: number;                // Default: 2000ms
  maxQueueSize?: number;                // Default: 1000

  // Deduplication
  enableDeduplication?: boolean;        // Default: true (by eventId)
  maxDeduplicationCacheSize?: number;   // Default: 10000

  // Middleware
  middleware?: Middleware[];             // Event transformers

  // Debug
  debug?: boolean;                      // Enable debug logging
  logger?: Logger;                      // Custom logger instance
}
```

## Methods

| Method | Signature | Description |
|--------|-----------|-------------|
| `track` | `(event: CustomEventInput) => Promise<EventResponse>` | Track a single event |
| `batch` | `(events: BatchEventInput[]) => Promise<BatchEventResponse>` | Send up to 100 events at once |
| `flush` | `() => Promise<BatchEventResponse>` | Force-send all queued events |
| `setGlobalProperties` | `(props: GlobalProperties) => void` | Merge properties into all future events |
| `getGlobalProperties` | `() => GlobalProperties` | Get current global properties |
| `clearGlobalProperties` | `() => void` | Remove all global properties |
| `addMiddleware` | `(fn: Middleware) => void` | Add event transformer/filter |
| `clearMiddleware` | `() => void` | Remove all middleware |
| `getDeduplicationCacheSize` | `() => number` | Get dedup cache size |
| `clearDeduplicationCache` | `() => void` | Clear dedup cache |

## Event Input

```typescript
interface CustomEventInput {
  name: string;                          // Required
  properties?: Record<string, unknown>;
  websiteId?: string | null;             // Override default
  namespace?: string | null;             // Override default
  source?: string | null;                // Override default
  eventId?: string;                      // For deduplication
  anonymousId?: string | null;
  sessionId?: string | null;
  timestamp?: number | null;             // Milliseconds
}
```

## Middleware

Middleware runs before deduplication. Return `null` to drop the event, or return a modified event.

```typescript
type Middleware = (event: BatchEventInput) => BatchEventInput | null | Promise<BatchEventInput | null>;

// Add custom field
client.addMiddleware((event) => {
  event.properties = { ...event.properties, processed: true };
  return event;
});

// Drop events
client.addMiddleware((event) => {
  if (event.name === "unwanted_event") return null;
  return event;
});
```

## Deduplication

Events with the same `eventId` are automatically deduplicated. The cache uses FIFO eviction at `maxDeduplicationCacheSize` (default 10,000).

```typescript
// These will only send once
await client.track({ name: "payment", eventId: "pay_123" });
await client.track({ name: "payment", eventId: "pay_123" }); // Deduplicated
```

## Serverless Usage

Always flush before the process exits:

```typescript
export async function handler(event) {
  const client = new Databuddy({ apiKey: process.env.DATABUDDY_API_KEY! });
  await client.track({ name: "lambda_invocation" });
  await client.flush(); // Ensure events are sent
  return { statusCode: 200 };
}
```

## Server-Side Feature Flags

```typescript
import { createServerFlagsManager } from "@databuddy/sdk/node";

const flags = createServerFlagsManager({
  clientId: process.env.DATABUDDY_CLIENT_ID!,
  autoFetch: true, // Default: false for server
});

await flags.waitForInit();

const flag = await flags.getFlag("my-feature");
console.log(flag.enabled, flag.value);

const value = flags.getValue("pricing-tier", "free");
```

See [flags skill](../databuddy-flags/SKILL.md) for full feature flags documentation.

## Authentication

The Node.js SDK authenticates via `Authorization: Bearer` header using the configured `apiKey` (read from `DATABUDDY_API_KEY` env var).

Events are sent to `POST /track` on the configured `apiUrl` (default: `https://basket.databuddy.cc`). Single events send one object; batches send an array.

> Never hardcode API keys. Always use `process.env.DATABUDDY_API_KEY`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/databuddy-analytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
