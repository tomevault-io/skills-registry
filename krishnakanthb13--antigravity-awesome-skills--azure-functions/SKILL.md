---
name: azure-functions
description: Expert patterns for Azure Functions development including isolated worker model, Durable Functions orchestration, cold start optimization, and production patterns. Covers .NET, Python, and Node.js ... Use when this capability is needed.
metadata:
  author: krishnakanthb13
---

# Azure Functions

## Patterns

### Isolated Worker Model (.NET)

Modern .NET execution model with process isolation

### Node.js v4 Programming Model

Modern code-centric approach for TypeScript/JavaScript

### Python v2 Programming Model

Decorator-based approach for Python functions

## Anti-Patterns

### ❌ Blocking Async Calls

### ❌ New HttpClient Per Request

### ❌ In-Process Model for New Projects

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | high | ## Use async pattern with Durable Functions |
| Issue | high | ## Use IHttpClientFactory (Recommended) |
| Issue | high | ## Always use async/await |
| Issue | medium | ## Configure maximum timeout (Consumption) |
| Issue | high | ## Use isolated worker for new projects |
| Issue | medium | ## Configure Application Insights properly |
| Issue | medium | ## Check extension bundle (most common) |
| Issue | medium | ## Add warmup trigger to initialize your code |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---
> Source: [krishnakanthb13/antigravity-awesome-skills](https://github.com/krishnakanthb13/antigravity-awesome-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
