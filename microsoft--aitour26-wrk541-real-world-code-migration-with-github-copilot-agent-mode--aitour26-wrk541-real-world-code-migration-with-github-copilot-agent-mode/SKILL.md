---
name: create-csharp-minimal-api
description: Create or extend the C# Minimal API migration project. Use for scaffolding Program.cs, DTOs, services, weather.json loading, and the first safe implementation steps. Use when this capability is needed.
metadata:
  author: microsoft
---

# Create C# Minimal API

## When to Use
- When initializing the C# migration project
- When adding the next C# endpoint
- When introducing DTOs or services to support parity
- When wiring weather.json data into the C# application

## Procedure
1. Confirm the specific endpoint or scope to implement.
2. Read the Python source and related tests first.
3. Review the checklist in [project-checklist](./assets/project-checklist.md).
4. Generate or edit only the minimum required C# files.
5. Stop after a small milestone and suggest the narrowest validation step.

## Rules
- Use ASP.NET Core Minimal APIs.
- Keep changes incremental and reviewable.
- Prefer `System.Text.Json` for JSON handling.
- Avoid broad rewrites when only one endpoint is being added.
- Preserve response behavior unless the task explicitly allows change.

## Output
- Files created or edited
- Scope implemented
- Remaining TODOs
- Recommended validation command

---
> Source: [microsoft/aitour26-WRK541-real-world-code-migration-with-github-copilot-agent-mode](https://github.com/microsoft/aitour26-WRK541-real-world-code-migration-with-github-copilot-agent-mode) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
