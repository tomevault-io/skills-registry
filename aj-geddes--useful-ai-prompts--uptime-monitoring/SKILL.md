---
name: uptime-monitoring
description: > Use when this capability is needed.
metadata:
  author: aj-geddes
---

# Uptime Monitoring

## Table of Contents

- [Overview](#overview)
- [When to Use](#when-to-use)
- [Quick Start](#quick-start)
- [Reference Guides](#reference-guides)
- [Best Practices](#best-practices)

## Overview

Set up comprehensive uptime monitoring with health checks, status pages, and incident tracking to ensure visibility into service availability.

## When to Use

- Service availability tracking
- Health check implementation
- Status page creation
- Incident management
- SLA monitoring

## Quick Start

Minimal working example:

```javascript
// Node.js health check
const express = require("express");
const app = express();

app.get("/health", (req, res) => {
  res.json({
    status: "ok",
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
  });
});

app.get("/health/deep", async (req, res) => {
  const health = {
    status: "ok",
    checks: {
      database: "unknown",
      cache: "unknown",
      externalApi: "unknown",
    },
  };

  try {
    const dbResult = await db.query("SELECT 1");
    health.checks.database = dbResult ? "ok" : "error";
// ... (see reference guides for full implementation)
```

## Reference Guides

Detailed implementations in the `references/` directory:

| Guide | Contents |
|---|---|
| [Health Check Endpoints](references/health-check-endpoints.md) | Health Check Endpoints |
| [Python Health Checks](references/python-health-checks.md) | Python Health Checks |
| [Uptime Monitor with Heartbeat](references/uptime-monitor-with-heartbeat.md) | Uptime Monitor with Heartbeat |
| [Public Status Page API](references/public-status-page-api.md) | Public Status Page API |
| [Kubernetes Health Probes](references/kubernetes-health-probes.md) | Kubernetes Health Probes |

## Best Practices

### ✅ DO

- Implement comprehensive health checks
- Check all critical dependencies
- Use appropriate timeout values
- Track response times
- Store check history
- Monitor uptime trends
- Alert on status changes
- Use standard HTTP status codes

### ❌ DON'T

- Check only application process
- Ignore external dependencies
- Set timeouts too low
- Alert on every failure
- Use health checks for load balancing
- Expose sensitive information

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aj-geddes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
