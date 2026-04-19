---
name: aspnet-framework-docs
description: Official Microsoft Learn workflow for ASP.NET Framework and .NET Framework 4.x guidance. Use when answering questions about ASP.NET Framework apps (MVC 5, Web API 2, Web Forms), framework installation/version checks, deployment, API reference lookup, or support lifecycle decisions that must stay aligned with Microsoft documentation. Use when this capability is needed.
metadata:
  author: rscoopcur
---

# ASP.NET Framework Docs

## Overview

Use this skill to produce documentation-grounded answers for legacy ASP.NET Framework and .NET Framework 4.x work. Load `references/microsoft-learn-dotnet-framework.md` and prefer those links as canonical sources.

## Workflow

1. Identify stack and target runtime.
- Confirm whether the user means ASP.NET Framework (System.Web, MVC 5/Web API 2/Web Forms) versus ASP.NET Core.
- Detect target framework from `.csproj`, `packages.config`, or `web.config` when files are available.
- State assumptions explicitly if the target is not provided.

2. Route to the right documentation cluster.
- Use install/version pages for runtime detection, prerequisites, and blocked installation issues.
- Use guide/deployment pages for architecture, configuration, and rollout questions.
- Use support-policy pages for lifecycle and package-support questions.

3. Build response with version and lifecycle context.
- Include concrete version data that affects recommendations (for example .NET Framework 4.8.1 status).
- Use absolute dates for lifecycle statements when available.
- Add direct links to the specific documentation pages used.

4. Produce implementation guidance.
- Start with the smallest safe change for legacy systems.
- Separate short-term maintenance advice from migration advice.
- Keep ASP.NET Core migration suggestions optional unless the user asks for modernization.

## Guardrails

- Avoid mixing ASP.NET Core instructions into ASP.NET Framework implementation steps.
- Re-check lifecycle details on official policy pages for any "latest support" request.
- Prefer Microsoft Learn and official .NET policy pages over blog/community content.

## Reference File

- `references/microsoft-learn-dotnet-framework.md`
  Load this first for link routing, support notes, and recommended search patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rscoopcur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
