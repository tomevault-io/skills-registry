---
name: documentation-sync
description: Keep template-facing documentation and generated artifacts aligned with code changes. Use when this capability is needed.
metadata:
  author: VilnaCRM-Org
---

# Documentation Sync

## Documentation Surfaces In This Template

- `README.md`
- `CONTRIBUTING.md`
- `SECURITY.md`
- `AGENTS.md`
- `CLAUDE.md`
- `.github/openapi-spec/spec.yaml`
- `.github/graphql-spec/spec`
- `workspace.dsl`

## Update This Material When

- local commands change
- workflow or contributor expectations change
- API surfaces change
- architecture changes
- new agent-support files or conventions are added

## Typical Sync Patterns

### Makefile or workflow change

- update `README.md`
- update contributor guidance if review expectations changed

### API change

- regenerate OpenAPI and GraphQL snapshots
- update `README.md` if user-visible commands or examples changed

### Architecture change

- update `workspace.dsl`
- update `README.md` if the template structure guidance changed

## Minimum Verification

- links and file references point to real paths
- command names match the current `Makefile`
- generated artifacts were refreshed in the same PR as the behavior change

---
> Source: [VilnaCRM-Org/php-service-template](https://github.com/VilnaCRM-Org/php-service-template) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
