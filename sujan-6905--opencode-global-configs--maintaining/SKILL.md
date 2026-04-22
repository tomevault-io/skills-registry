---
name: maintaining
description: Maintenance skill for dependency, ignore-file, config, and environment-example hygiene Use when this capability is needed.
metadata:
  author: sujan-6905
---

# Maintaining Skill

## Use This Skill For

- dependency updates
- ignore-file maintenance
- config hygiene
- keeping setup documentation and examples current

## Do Not Use This Skill For

- large feature development
- architectural redesign that goes beyond maintenance hygiene

## Maintenance Workflow

1. Identify which source of truth changed.
2. Update the related config or dependency manifest.
3. Update ignore files if new generated or secret files are involved.
4. Update `.env.example` when environment variables are added, removed, or renamed.
5. Update README when setup or behavior changes.

## Dependency Rules

- Keep manifests and lockfiles aligned.
- Prefer stable releases unless the task explicitly needs otherwise.
- Remove stale dependencies when they are clearly no longer used.

## Ignore File Rules

Use nested-safe patterns when appropriate.

### Typical `.gitignore` entries

```gitignore
**/node_modules/
**/.next/
**/.turbo/
**/dist/
**/build/
**/__pycache__/
**/.pytest_cache/
.history/
**/.env
**/.env.*
```

### Typical `.dockerignore` entries

```dockerignore
**/node_modules/
**/.next/
**/.turbo/
**/dist/
**/build/
**/__pycache__/
.history/
**/.git/
**/.env
**/.env.*
```

## Environment Rules

- Never read `.env` directly.
- Never edit `.env` directly.
- Use `.env.example` as the checked-in contract for environment variables.
- Assume the same keys exist in the local `.env`.
- Document any required variables in README.

## Maintenance Checklist

- dependency manifests updated
- lockfiles updated when needed
- ignore files reflect new outputs or secrets
- `.env.example` matches current requirements
- README reflects setup changes

## Done Criteria

- config and docs match the current codebase
- no secret-handling regression is introduced
- maintenance changes are small, explicit, and reversible

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sujan-6905) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
