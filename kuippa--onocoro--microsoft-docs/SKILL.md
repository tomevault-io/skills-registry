---
name: microsoft-docs
description: Query official Microsoft documentation to understand concepts, find tutorials, and learn how services work. Use for Azure, .NET, Microsoft 365, Windows, Power Platform, and all Microsoft technologies. Get accurate, current information from learn.microsoft.com and other official Microsoft websites—architecture overviews, quickstarts, configuration guides, limits, and best practices. **OnoCoro Customized for Unity/C# Development.** Use when this capability is needed.
metadata:
  author: kuippa
---

# Microsoft Docs (OnoCoro Customized)

Query official Microsoft documentation to understand concepts, find tutorials, and learn how services work. This skill is customized for **OnoCoro C# development with Unity 6.3 and PLATEAU SDK integration**.

## Tools

| Tool | Use For |
|------|---------|
| `microsoft_docs_search` | Find documentation—concepts, guides, tutorials, configuration |
| `microsoft_docs_fetch` | Get full page content (when search excerpts aren't enough) |

## When to Use

- **Understanding C# concepts** — "How does async/await work in C#?", "What are nullable reference types?"
- **Learning Unity APIs** — "Unity Transform overview", "How does Instantiate work?"
- **PLATEAU SDK documentation** — "PLATEAU SDK CityGML loading", "Coordinate transformation"
- **Finding tutorials** — "quickstart", "getting started", "step-by-step"
- **Configuration options** — "Unity project settings", ".NET configuration"
- **Limits & quotas** — "Unity Scene size limits", "Asset loading quotas"
- **Best practices** — "C# code style guidelines", "Unity performance best practices"

## Query Effectiveness

Good queries are specific:

```
# ❌ Too broad
"C# async"
"Unity Transform"

# ✅ Specific
"C# async await patterns best practices"
"Unity Transform.Find performance guidelines"
"Microsoft.Extensions.DependencyInjection C# example"
"Entity Framework Core mapping strategies"
```

Include context:
- **Version** when relevant (`.NET 8`, `C# 12`, `Unity 6.3`)
- **Task intent** (`quickstart`, `tutorial`, `overview`, `limits`, `performance`)
- **Platform** for multi-platform docs (`Windows`, `Linux`, `.NET Standard`)

## OnoCoro-Specific Queries

### Recovery Phase Development

```
microsoft_docs_search(query: "C# null-safety best practices .NET 8")
microsoft_docs_search(query: "Unity Serializable fields guidelines")
microsoft_docs_search(query: ".NET exception handling patterns")
microsoft_docs_search(query: "C# async/await best practices Recovery phase")
```

### PLATEAU SDK & Geospatial

```
microsoft_docs_search(query: "C# coordinate transformation libraries")
microsoft_docs_search(query: ".NET geographic coordinate systems")
microsoft_docs_search(query: "C# mesh generation Unity Procedural")
```

### Asset Management

```
microsoft_docs_search(query: "C# resource management patterns .NET")
microsoft_docs_search(query: "C# caching strategies performance")
microsoft_docs_search(query: "Unity prefab instantiation best practices")
```

## When to Fetch Full Page

Fetch after search when:
- **Tutorials** — need complete step-by-step instructions
- **Configuration guides** — need all options listed
- **Deep dives** — user wants comprehensive coverage
- **Search excerpt is cut off** — full context needed
- **Performance considerations** — need complete tuning guide

## Why Use This

- **Accuracy** — live docs, not training data that may be outdated
- **Completeness** — tutorials have all steps, not fragments
- **Authority** — official Microsoft documentation
- **OnoCoro Context** — customized for C#/Unity/.NET development in Recovery phase

## Examples

### Finding C# Best Practices

```
Question: "How should I handle null checking in Recovery phase code?"

Action:
1. microsoft_docs_search(query: "C# null-safety guidelines .NET best practices")
2. microsoft_docs_fetch(url: "[result URL]") if more detail needed
3. Apply pattern to code review
```

### Understanding Unity + C# Integration

```
Question: "What's the best way to manage Serializable fields in Unity?"

Action:
1. microsoft_docs_search(query: "C# Serializable attributes Unity guidelines")
2. microsoft_docs_fetch(url: "[result URL]")
3. Review [AGENTS.md](../../../AGENTS.md) for OnoCoro compliance
```

### PLATEAU Data Processing

```
Question: "How do I efficiently transform geographic coordinates in C#?"

Action:
1. microsoft_docs_search(query: ".NET geographic coordinate transformation libraries")
2. microsoft_docs_search(query: "C# numeric precision coordinate systems")
3. Reference [docs/architecture.md](../../../docs/architecture.md) for integration
```

---

**Note**: This skill is optimized for OnoCoro development. For general .NET/Azure documentation, refer to official [learn.microsoft.com](https://learn.microsoft.com).

**Last Updated**: 2026-01-20

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kuippa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
