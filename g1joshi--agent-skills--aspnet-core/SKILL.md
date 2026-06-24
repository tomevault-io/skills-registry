---
name: aspnet-core
description: ASP.NET Core cross-platform .NET framework with Blazor support. Use for C# web apps. Use when this capability is needed.
metadata:
  author: g1joshi
---

# ASP.NET Core

Cross-platform, high-performance framework for building modern web apps.

## When to Use

- High-performance APIs (one of the fastest benchmarks)
- Enterprise applications
- Microservices
- Real-time apps (SignalR)

## Quick Start

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/", () => "Hello World!");

app.Run();
```

## Core Concepts

### Middleware Pipeline

Request processing pipeline composed of delegates.

### Dependency Injection

Built-in DI container, a core citizen of the framework.

### Kestrel

Cross-platform web server for ASP.NET Core.

## Best Practices

**Do**:

- Use the built-in DI
- Use asynchronous controllers (`async Task<IActionResult>`)
- Use Environment Variables for config settings

**Don't**:

- Block asynchronous code (`.Result` or `.Wait()`)
- Store large objects in Session state

## References

- [ASP.NET Core Docs](https://learn.microsoft.com/en-us/aspnet/core/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/g1joshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
