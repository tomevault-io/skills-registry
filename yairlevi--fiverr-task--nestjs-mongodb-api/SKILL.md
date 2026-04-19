---
name: nestjs-mongodb-api
description: | Use when this capability is needed.
metadata:
  author: yairlevi
---

# NestJS + MongoDB API Skill

This skill provides scaffolding templates, architectural patterns, and best practices for building production-grade NestJS REST APIs with MongoDB and Mongoose.

## Quick Scaffolding Commands

When asked to generate a new module or feature, follow these steps:

1. Read `references/architecture.md` for the clean architecture layer structure
2. Read `references/naming-conventions.md` for file and class naming rules
3. Use `scripts/scaffold-module.sh` to generate the folder skeleton
4. Fill in each file using the templates in `references/templates.md`

## Tech Stack

- **Framework**: NestJS 10+
- **Database**: MongoDB 7+
- **ODM**: Mongoose 8+ via `@nestjs/mongoose`
- **Validation**: `class-validator` + `class-transformer`
- **API Docs**: Swagger via `@nestjs/swagger`
- **Config**: `@nestjs/config` with Joi validation

## Key References

- `references/architecture.md` — Clean architecture layer breakdown
- `references/naming-conventions.md` — File, class, and variable naming rules
- `references/templates.md` — Code templates for all layer files

## Standard File Creation Checklist

For every new feature `<Feature>`, create:

```
src/
├── domain/
│   ├── entities/<feature>.entity.ts
│   ├── repositories/i-<feature>.repository.ts
│   └── exceptions/<feature>.exceptions.ts
├── application/
│   ├── use-cases/create-<feature>.use-case.ts
│   ├── use-cases/find-<feature>.use-case.ts
│   ├── use-cases/update-<feature>.use-case.ts
│   ├── use-cases/delete-<feature>.use-case.ts
│   └── dtos/<feature>.dto.ts
├── infrastructure/
│   └── database/
│       ├── schemas/<feature>.schema.ts
│       ├── repositories/mongoose-<feature>.repository.ts
│       └── mappers/<feature>.mapper.ts
├── presentation/
│   └── controllers/<feature>.controller.ts
└── <feature>.module.ts
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yairlevi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
