---
name: codegen
description: description: Regenerate TypeScript models and API services from the backend. Use this after any backend API change including new/modified endpoints, DTO changes, route changes, or controller modifications. Use when this capability is needed.
metadata:
  author: sitkatech
---
---
name: codegen
description: Regenerate TypeScript models and API services from the backend. Use this after any backend API change including new/modified endpoints, DTO changes, route changes, or controller modifications.
allowed-tools: [Bash(dotnet build:*), Bash(npm run gen-model:*)]
---

Run the full code generation pipeline to sync the Angular frontend with backend API changes.

## Steps

1. Build the API in Debug configuration to regenerate `swagger.json`:
   ```
   dotnet build Neptune.API/Neptune.API.csproj -c Debug
   ```
   Verify the build succeeds before continuing.

2. Regenerate TypeScript models and API services from the updated swagger spec:
   ```
   cd Neptune.Web && npm run gen-model
   ```

3. Report what changed by running `git diff --stat Neptune.Web/src/app/shared/generated/` and `git diff --stat Neptune.API/swagger.json` to summarize the regenerated files.

## Notes

- The Debug build configuration triggers Swagger JSON generation via a post-build step.
- Generated TypeScript files go to `Neptune.Web/src/app/shared/generated/` — never edit these directly.
- If new API services were generated, they may need to be imported in Angular components.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sitkatech) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
