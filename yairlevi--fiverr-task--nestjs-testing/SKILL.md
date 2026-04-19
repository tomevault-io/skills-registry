---
name: nestjs-testing
description: | Use when this capability is needed.
metadata:
  author: yairlevi
---

# NestJS Testing Skill

This skill provides patterns, utilities, and templates for writing comprehensive tests in NestJS using **Jest** and **Supertest**.

## Quick Reference

- For unit test patterns → read `references/unit-testing.md`
- For e2e / Supertest patterns → read `references/e2e-testing.md`
- For mocking helpers → read `references/mocking.md`
- To generate a test file → run `scripts/generate-test.sh <path-to-source-file>`

## Test File Location Convention

| Source file | Test file |
|-------------|-----------|
| `src/domain/entities/user.entity.ts` | `src/domain/entities/user.entity.spec.ts` |
| `src/application/use-cases/create-user.use-case.ts` | `src/application/use-cases/create-user.use-case.spec.ts` |
| `src/infrastructure/database/repositories/mongoose-user.repository.ts` | `src/infrastructure/database/repositories/mongoose-user.repository.spec.ts` |
| `src/presentation/controllers/users.controller.ts` | `test/users.e2e-spec.ts` |

## Coverage Targets

- Domain entities: 100%
- Use cases: 100%
- Repository implementations: ≥ 90%
- Controllers: tested via e2e only

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yairlevi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
