---
name: logfire-observability
description: Full OpenClaw observability in Pydantic Logfire — agent traces, tool calls, metrics, and logs via OpenTelemetry Use when this capability is needed.
metadata:
  author: rita-aga
---

# Logfire Observability

Sends OpenClaw agent traces, tool calls, and messages to [Pydantic Logfire](https://logfire.pydantic.dev) via OpenTelemetry.

## Install the plugin

```bash
openclaw plugins install openclaw-logfire-observability
```

## Configure

Add to your `openclaw.json`:

```json
{
  "diagnostics": {
    "enabled": true,
    "otel": {
      "enabled": true,
      "endpoint": "https://logfire-us.pydantic.dev",
      "headers": {
        "Authorization": "Bearer pylf_v1_us_YOUR_TOKEN_HERE"
      },
      "serviceName": "openclaw",
      "traces": true,
      "metrics": true,
      "logs": true
    }
  },
  "plugins": {
    "entries": {
      "openclaw-logfire-observability": {
        "enabled": true,
        "config": {
          "logfireToken": "pylf_v1_us_YOUR_TOKEN_HERE"
        }
      },
      "diagnostics-otel": {
        "enabled": true
      }
    }
  }
}
```

Replace `YOUR_TOKEN_HERE` with your Logfire write token (from Settings > Write Tokens in your Logfire project).

Then restart OpenClaw.

## What you get

- **Agent traces** with parent-child nesting (agent.run → tool.* spans)
- **Metrics**: token usage, cost, duration, queue health, session state
- **Logs**: full OpenClaw log forwarding
- **Diagnostic traces**: model usage, webhooks, message processing

See the full [README](https://github.com/rita-aga/openclaw-logfire-observability) for architecture details, config options, and Logfire query examples.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rita-aga) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
