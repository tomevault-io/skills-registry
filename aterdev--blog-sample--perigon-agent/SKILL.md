---
name: perigon-agent
description: Pointers for Copilot/agents to apply Perigon conventions Use when this capability is needed.
metadata:
  author: aterdev
---
## When to use
- Need project rules, locations, or docs before generating code.

## Usage
- Start with .github/copilot-instructions.md (global rules: accuracy first, no builds unless asked, check diagnostics).
- Backend map: definitions in src/Definition/{Entity,EntityFramework,Share,ServiceDefaults}; managers/DTOs in src/Modules/{Mod}/{Managers,Models}; controllers in src/Services/*/Controllers; host in src/AppHost.
- Frontend map: Angular app in src/ClientApp/WebApp (routes app/app.routes.ts, services app/services, shared components app/share/components, i18n assets/i18n).
- Key docs: Development-Conventions, Manager-Business-Logic, Controller-APIs, Database, Data-Access, Directory-Structure at https://dusi.dev/docs/Perigon/en-US/10.0/…
- Behavior defaults: RESTful APIs; ManagerBase pattern; Code First EF; Guid v7 IDs; BusinessException/Problem for errors; select projections over heavy Include; avoid manager cross-calls; no ApiResponse wrappers.
- When unclear: ask for entity/DTO details and target module/service; do not assume; avoid auto-running builds/migrations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aterdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
