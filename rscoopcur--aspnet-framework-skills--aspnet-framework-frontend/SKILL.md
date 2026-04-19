---
name: aspnet-framework-frontend
description: Expert workflow for ASP.NET Framework (.NET Framework 4.x) web apps using Vue 2, Vue 3, or Alpine.js with Razor and Web API. Use when integrating frontend frameworks into ASP.NET Framework, choosing framework patterns, implementing API and anti-forgery flows, troubleshooting runtime/version setup, or answering support and lifecycle questions with official Microsoft Learn .NET Framework documentation. Use when this capability is needed.
metadata:
  author: rscoopcur
---

# ASP.NET Framework Frontend

## Overview

Use this skill for ASP.NET Framework frontend architecture and implementation. Keep framework-specific code guidance in sync with official Microsoft Learn documentation before giving final recommendations.

## Core Workflow

1. Identify stack and runtime.
- Confirm ASP.NET Framework (MVC 5, Web API 2, Web Forms, System.Web) versus ASP.NET Core.
- Detect target runtime from `.csproj`, `packages.config`, or `web.config`.
- State assumptions when the runtime is unknown.

2. Choose frontend strategy.
- Choose Vue 2 for legacy maintenance and Options API continuity.
- Choose Vue 3 for new/expanded client-side features and composables.
- Choose Alpine.js for lightweight progressive enhancement in server-rendered pages.
- Use Vue + Alpine only with strict root-element isolation.

3. Implement ASP.NET integration path.
- Define bundles in `BundleConfig.cs` and toggle optimization by environment.
- Pass server data to client using strongly typed models or serialized payloads.
- Use Web API endpoints with anti-forgery validation on state-changing requests.
- Centralize fetch helpers and error handling in shared JavaScript utilities.

4. Validate against official documentation.
- Load `references/microsoft-learn-dotnet-framework.md` for install/version/support questions.
- Confirm lifecycle and support answers against policy pages before finalizing.
- Use absolute dates when reporting end-of-support milestones.

5. Deliver response.
- Provide minimal safe implementation steps first.
- Separate maintenance guidance from optional migration guidance.
- Add direct documentation links used for the recommendation.

## Guardrails

- Do not mix ASP.NET Core implementation details into ASP.NET Framework instructions.
- Do not use `v-html` or `x-html` for untrusted input.
- Do not omit anti-forgery token handling for `POST`, `PUT`, or `DELETE`.
- Do not recommend Vue 2 for new greenfield work unless constraints require it.

## Reference Loading Order

1. `references/aspnet-integration.md`
- Use for ASP.NET MVC/Web API + frontend integration structure.

2. Framework-specific reference
- `references/vue2-best-practices.md`
- `references/vue3-best-practices.md`
- `references/alpinejs-best-practices.md`

3. Official documentation map
- `references/microsoft-learn-dotnet-framework.md`
- Use for runtime versions, installation, API browser, and lifecycle policy routing.

## Quick Checklist

- Confirm runtime target (.NET Framework 4.x variant).
- Select Vue 2, Vue 3, or Alpine with explicit rationale.
- Implement bundle strategy and server-to-client data transfer.
- Include anti-forgery flow in API calls.
- Verify support/lifecycle answers with official policy links.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rscoopcur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
