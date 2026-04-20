---
name: entity-generator
description: Scaffolds a MikroORM entity (extending CustomBaseEntity), a dedicated repository, and registers them in the entity registry. Use when you need to add a new data model to the database. Use when this capability is needed.
metadata:
  author: ctrlaltelite-devs
---

# Entity Generator

This skill automates the creation of a new database entity and its corresponding repository, following the project's data architecture.

## Workflow

1.  **Identify the entity name**: Use kebab-case (e.g., `user-profile`, `course-enrollment`).
2.  **Execute the generator script**: Use the bundled script to create the files and update the registry.
3.  **Verify the output**: Check `src/entities/`, `src/repositories/`, and `src/entities/index.entity.ts`.
4.  **Format the code**: Run `npm run lint` or `npm run format`.

## Usage

Run the following command from the project root:

```bash
node .gemini/skills/entity-generator/scripts/generate_entity.cjs <entity-name>
```

### Example

To create a `course-log` entity:

```bash
node .gemini/skills/entity-generator/scripts/generate_entity.cjs course-log
```

This will:

- Create `src/entities/course-log.entity.ts`.
- Create `src/repositories/course-log.repository.ts`.
- Update `src/entities/index.entity.ts` to include the new entity.

## Standards Applied

- **Base Class**: Extends `CustomBaseEntity`.
- **Primary Key/Timestamps**: Inherited from `CustomBaseEntity` (id, createdAt, updatedAt, deletedAt).
- **Repository**: Uses `@Entity({ repository: () => ... })` to link the custom repository.
- **File Naming**: kebab-case.
- **Class Naming**: PascalCase.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ctrlaltelite-devs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
