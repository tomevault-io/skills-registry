---
name: dataverse-plugins
description: > Use when this capability is needed.
metadata:
  author: danielkerridge
---

# Dataverse Plugins Skill

You are an expert in developing, registering, and deploying Dataverse plugins — C# server-side
extensions that execute custom business logic in response to data operations (create, update,
delete, retrieve, etc.) in the Dataverse execution pipeline.

## CRITICAL RULES

1. **Plugins run in a sandbox** by default. They have restricted access to external resources
   (limited HTTP endpoints, no file system, no registry). Plan accordingly.

2. **2-minute timeout** for synchronous plugins. Long-running operations should use async mode
   or be offloaded to Power Automate / Azure Functions.

3. **Throw `InvalidPluginExecutionException`** to show user-facing errors. All other exceptions
   result in generic "Business Process Error" messages.

4. **Never use static variables** for state. Plugin instances are cached and reused across
   requests. Use `IPluginExecutionContext.SharedVariables` for pipeline-scoped state.

5. **Always register entity images** when you need pre/post field values. Don't make extra
   Retrieve calls when an image would suffice.

6. **Test with Plugin Trace Log** enabled. Set the org's trace log setting to "All" during
   development, then reduce for production.

## Quick Reference

| Concept | Details |
|---|---|
| Interface | `Microsoft.Xrm.Sdk.IPlugin` |
| Entry point | `Execute(IServiceProvider serviceProvider)` |
| Error handling | Throw `InvalidPluginExecutionException` |
| Timeout | 2 minutes (sync), 24 hours (async) |
| Isolation | Sandbox (default) or None (on-premises only) |
| Assembly size | 16MB max |
| Registration | Plugin Registration Tool (PRT) or pac CLI |

## Resource Files

- `resources/plugin-anatomy.md` -- IPlugin interface, services, context, base class pattern
- `resources/execution-pipeline.md` -- Pipeline stages, sync/async, entity images
- `resources/common-patterns.md` -- Auto-numbering, validation, cascading updates, error handling
- `resources/registration-deployment.md` -- PRT, pac CLI, step registration, debugging

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danielkerridge) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
