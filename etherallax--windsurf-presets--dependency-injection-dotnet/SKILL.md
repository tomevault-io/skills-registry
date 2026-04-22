---
name: dependency-injection-dotnet
description: Use when applying .NET dependency injection patterns, service lifetimes, and composition via Microsoft.Extensions.DependencyInjection or equivalent.
metadata:
  author: etherallax
---

- If the current project is not .NET-based, ignore this skill.
- If the current project is not .NET-based, ignore this skill.
- Use constructor injection as the default pattern
- Prefer explicit service registration over service locators
- Respect service lifetimes (Singleton, Scoped, Transient)
- Avoid static/global dependencies where possible
- Favor DI-friendly APIs and testable design

Framework guidance:
- .NET: Microsoft.Extensions.DependencyInjection
- Generic Host patterns are acceptable when appropriate
- Do not introduce alternative DI containers unless explicitly approved in PROJECT or PRD

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/etherallax) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
