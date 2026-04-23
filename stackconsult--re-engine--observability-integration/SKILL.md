---
name: observability-integration
description: Implementation guide for production monitoring, error tracking, and health checks using Sentry and Pino. Use when this capability is needed.
metadata:
  author: stackconsult
---

# Observability Integration Skill

This skill outlines the steps to implement professional-grade monitoring and observability for the RE-Engine production stack.

## Objective
Ensure the system is "visible" by tracking errors in real-time, providing health signals for load balancers, and centralizing logs for rapid debugging.

## 🛠️ Implementation Guide

### 1. Sentry Integration (`observability/sentry.ts`)
Setup global error tracking.

```typescript
import * as Sentry from "@sentry/node";

export function initSentry(dsn: string) {
  Sentry.init({
    dsn,
    environment: process.env.NODE_ENV,
    tracesSampleRate: 1.0,
  });
}
```

### 2. Health Check Endpoints (`api/health.ts`)
Implement standard `/health` and `/ready` endpoints.

```typescript
import { Router } from "express";

export const healthRouter = Router();

healthRouter.get("/health", (req, res) => {
  res.status(200).json({ status: "ok", timestamp: new Date().toISOString() });
});

healthRouter.get("/ready", async (req, res) => {
  // Check DB connection, MCP server status, etc.
  const isDbUp = await checkDbConnection();
  if (isDbUp) {
    res.status(200).json({ status: "ready" });
  } else {
    res.status(503).json({ status: "not ready" });
  }
});
```

### 3. Log Centralization
Update the `Logger` class to use a production transport (e.g., pumping to CloudWatch or a log aggregator via HTTP).

```typescript
// Example: Adding a production transport to Pino
const transport = pino.transport({
  target: 'pino/file',
  options: { destination: '/var/log/reengine/app.log' } // Or an external service
});
```

## ⚠️ Best Practices
- **Custom Exceptions**: Use custom error classes to categorize issues (e.g., `DatabaseError`, `MCPTimeoutError`) for better Sentry grouping.
- **Contextual Data**: Always attach relevant metadata (userId, organizationId) to Sentry events without logging PII (Personally Identifiable Information).
- **Metric Thresholds**: Define alerts for when the `/health` endpoint fails more than X times in Y minutes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stackconsult) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
