---
name: migration
description: Manage EF Core database migrations. Use when adding, listing, or removing migrations for the workflow engine. Use when this capability is needed.
metadata:
  author: Altinn
---

## Add a new migration

```bash
dotnet ef migrations add <MigrationName> \
  --project src/WorkflowEngine.Data \
  --startup-project tests/WorkflowEngine.TestApp
```

After generating, run `dotnet csharpier format` on the new migration files in `src/WorkflowEngine.Data/Migrations/`.

## List existing migrations

```bash
dotnet ef migrations list \
  --project src/WorkflowEngine.Data \
  --startup-project tests/WorkflowEngine.TestApp
```

## Remove the last migration (if not yet applied)

```bash
dotnet ef migrations remove \
  --project src/WorkflowEngine.Data \
  --startup-project tests/WorkflowEngine.TestApp
```

## Important notes

- Migrations are applied automatically on application startup via `DbMigrationService` — there is no need to run `dotnet ef database update` manually.
- Migration files live in `src/WorkflowEngine.Data/Migrations/`.
- The DbContext is `EngineDbContext` in `WorkflowEngine.Data`.
- Always format generated migration files with CSharpier before committing.

---
> Source: [Altinn/altinn-studio](https://github.com/Altinn/altinn-studio) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-03 -->
