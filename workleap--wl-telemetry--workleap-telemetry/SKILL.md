---
name: workleap-telemetry
description: | Use when this capability is needed.
metadata:
  author: workleap
---

# Workleap Telemetry (wl-telemetry)

`@workleap/telemetry` is an umbrella package that integrates Honeycomb, LogRocket, and Mixpanel with consistent correlation IDs for unified debugging and analysis.

## Platform Roles

- **Honeycomb**: Distributed tracing and performance monitoring (LCP, CLS, INP)
- **LogRocket**: Session replay and frontend debugging
- **Mixpanel**: Product analytics and event tracking

## Quick Start

```typescript
import { initializeTelemetry, TelemetryProvider } from "@workleap/telemetry/react";

const telemetryClient = initializeTelemetry("wlp", {
  logRocket: { appId: "your-app-id" },
  honeycomb: {
    namespace: "your-namespace",
    serviceName: "your-service",
    apiServiceUrls: [/.+/g],
    options: { proxy: "https://your-otel-proxy" }
  },
  mixpanel: {
    envOrTrackingApiBaseUrl: "production"
  }
});

<TelemetryProvider client={telemetryClient}>
  <App />
</TelemetryProvider>
```

## Critical Rules

1. **Use the umbrella package** — Import from `@workleap/telemetry`, not standalone packages like `@workleap/honeycomb` directly. Standalone packages bypass the automatic correlation ID propagation that ties all three platforms together.
2. **Do not invent APIs** — Only use APIs documented in the references. The model may hallucinate plausible-looking methods that don't exist.
3. **Let correlation happen automatically** — Never manually set Telemetry Id or Device Id, and never create your own `TelemetryContext` instances. The SDK manages the lifecycle and cross-tool propagation; manual overrides break the correlation chain.
4. **Use Noop clients outside production** — Use `NoopTelemetryClient` in Storybook and tests to avoid sending real telemetry data and to prevent initialization errors in non-browser environments.
5. **Protect user privacy** — Never log PII to LogRocket. Session replays are shared across teams and may be reviewed broadly; use `data-public`/`data-private` HTML attributes to control what gets recorded.
6. **productFamily is required** — `initializeTelemetry` requires `"wlp"` (Workleap Platform) or `"sg"` (ShareGate) as the first argument. This determines platform-specific defaults and routing.

## Reference Guide

Consult these references based on what you need:

- **`references/api.md`** — Function signatures, type definitions, and complete API surface for all clients and hooks. Start here when you need to know exactly what parameters a method accepts.
- **`references/integrations.md`** — Platform-specific configuration patterns, custom Honeycomb traces, LogRocket privacy controls, Mixpanel event tracking, and cross-platform correlation workflows. Start here for "how do I configure X" questions.
- **`references/examples.md`** — Copy-paste starter code for common scenarios: full app setup, Storybook decorators, test utilities, user identification, and troubleshooting. Start here for "show me how to do X" questions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/workleap) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
