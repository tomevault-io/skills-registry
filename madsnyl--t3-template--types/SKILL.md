---
name: prisma-type-settings
description: Define and organize TypeScript types for Prisma 7 queries using Prisma.validator and GetPayload, structured by domain under the types/ folder. Use when this capability is needed.
metadata:
  author: madsnyl
---

# Prisma 7 Type Settings (TypeScript)

You are an expert in **TypeScript types for Prisma 7** and how to structure them cleanly.

## Activation cues
Use this skill when the user asks to:
- create query payload types (with `select/include`)
- define API response types for endpoints
- standardize types in `types/` by domain
- avoid duplication and keep types aligned with Prisma schema

## Core rules
1. Prefer **Prisma-generated types** over hand-written shapes.
2. Use `Prisma.validator()` to define reusable `select/include` objects with exact types.
3. Use `Prisma.<Model>GetPayload<typeof args>` for derived result types.
4. Keep types **domain-scoped**: files live in `types/<domain>/...` (never one giant types file).

(See Prisma type safety + validator docs in `references/PRISMA7_CORE_REFERENCES.md`.)

## Folder conventions
Use this structure unless user already has another:
- `types/`
  - `billing/`
    - `invoice.types.ts`
  - `projects/`
    - `project.select.ts` (query arg objects)
    - `project.types.ts`  (payload/result types)
  - `users/`
    - `user.types.ts`

### Naming conventions
- `XSelect` / `XInclude` for arg objects
- `XListItem`, `XDetail`, `XWith...` for payload types
- `XCreateInput`, `XUpdateInput` for DTO-ish shapes (only if needed)

## Output format
When creating types, provide:
1. File path(s) under `types/<domain>/...`
2. Exported `select/include` objects
3. Exported `GetPayload` types
4. A short example query using those exports

## Examples

### Example: reusable selection + payload type
```ts
// types/projects/project.select.ts
import { Prisma } from "generated/prisma";

export const projectListSelect = Prisma.validator<Prisma.ProjectSelect>()({
  id: true,
  name: true,
  slug: true,
  createdAt: true,
  workspace: {
    select: { id: true, name: true },
  },
});
```

```ts
// types/projects/project.types.ts
import { Prisma } from "generated/prisma";
import { projectListSelect } from "./project.select";

export type ProjectListItem = Prisma.ProjectGetPayload<{
  select: typeof projectListSelect;
}>;
```

```ts
// usage
const projects = await prisma.project.findMany({
  where: { workspaceId },
  select: projectListSelect,
});
```

### Example: include payload for a detail view
```ts
// types/projects/project.types.ts
import { Prisma } from "generated/prisma";

export const projectDetailArgs = Prisma.validator<Prisma.ProjectDefaultArgs>()({
  include: {
    workspace: true,
    projectMembers: {
      include: { user: { select: { id: true, email: true } } },
    },
  },
});

export type ProjectDetail = Prisma.ProjectGetPayload<typeof projectDetailArgs>;
```

## Additional resources

- For complete Prisma docs details, see [reference.md](@.claude/skills/prisma/reference.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/madsnyl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
