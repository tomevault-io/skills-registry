---
name: dotnet
description: Best practices for .NET 10 development covering ASP.NET Core APIs, Entity Framework Core, Blazor, .NET MAUI, Avalonia UI, AI integration with Microsoft.Extensions.AI, and cloud-native apps with .NET Aspire. Use when working with C#, .NET projects, NuGet packages, or Microsoft .NET ecosystem. Use when this capability is needed.
metadata:
  author: expcat
---

# .NET Development Best Practices

Comprehensive guidance for modern .NET 10 development.

## When to Use

Activate when:

- Creating/modifying C#/.NET projects
- Building ASP.NET Core APIs or web apps
- Working with Entity Framework Core
- Developing Blazor components
- Building .NET MAUI cross-platform apps
- Building Avalonia cross-platform desktop/mobile apps
- Integrating AI with Microsoft.Extensions.AI
- Creating cloud-native apps with .NET Aspire

## Reference Files

Load specific references as needed:

| Reference                                 | Topics                                           |
| ----------------------------------------- | ------------------------------------------------ |
| [dotnet.md](references/dotnet.md)         | C# 14, project structure, DI, async, logging     |
| [aspnetcore.md](references/aspnetcore.md) | Minimal APIs, validation, OpenAPI, auth, caching |
| [efcore.md](references/efcore.md)         | DbContext, vector search, JSON, migrations       |
| [blazor.md](references/blazor.md)         | Components, state, forms, render modes           |
| [maui.md](references/maui.md)             | MVVM, Shell navigation, platform code            |
| [avalonia.md](references/avalonia.md)     | Cross-platform UI, MVVM, styles, themes          |
| [ai.md](references/ai.md)                 | Microsoft.Extensions.AI, Agent Framework, MCP    |
| [aspire.md](references/aspire.md)         | Orchestration, service discovery, telemetry      |

## Essential Packages

`Serilog.AspNetCore` `FluentValidation` `Mapster` `Polly` `MediatR` `CommunityToolkit.Mvvm` `Microsoft.Extensions.AI`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/expcat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
