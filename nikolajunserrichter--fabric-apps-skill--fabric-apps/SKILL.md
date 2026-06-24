---
name: fabric-apps
description: >- Use when this capability is needed.
metadata:
  author: NikolajUnserRichter
---

# Microsoft Fabric Apps (Rayfin)

## What this is

Fabric Apps (preview) is a Microsoft Fabric workload for building full-stack, data-driven web
apps. You write **TypeScript entity classes decorated with `@entity()`, `@text()`, `@role()`,
etc.**, and the **Rayfin CLI** compiles them into a managed **SQL database in Fabric**, a
**GraphQL Data API**, **row-level authorization**, and **static hosting** — all behind a single
app endpoint with **Microsoft Entra SSO**.

The whole platform is the "Rayfin" toolchain. Three pieces show up constantly:

- `@microsoft/rayfin-core` — decorators you use to define data models (`@entity`, `@text`, `@role`, …).
- `@microsoft/rayfin-client` — the type-safe `RayfinClient` your frontend uses to read/write data.
- `@microsoft/rayfin-cli` (`rayfin` / `npx rayfin`) — scaffold, run locally, apply schema, deploy.

Mental model of the data flow — keep this in mind, because every "how do I…" question maps to one stage:

```
TypeScript entity classes (rayfin/data/*.ts)
  → decorators (@entity, @text, @one, @role …)
    → CLI compiles to → SQL schema + GraphQL API + auth policies
      → RayfinClient (generated, type-safe) → your frontend (React/Vue/Svelte/…)
```

## When to reach for it (and when not)

Good fit: rapid prototypes, internal tools/dashboards, data-exploration UIs, and structured
backends for AI/agent apps — anything where you want a live, authenticated, data-backed app
without writing backend boilerplate.

Bad fit (tell the user up front, don't try to force it):

- **Not** Azure Service Fabric, Fabric notebooks/lakehouses/pipelines, or Power BI. If the user
  means one of those, this skill does not apply.
- No custom auth providers — only Entra SSO (deployed) and email/password (local dev only).
- No existing/external database — Fabric Apps owns the schema; you can't point it at one.
- No composite keys, no many-to-many (use a join entity), no stored procedures / multi-step
  transactions, TypeScript only for models.

## Before writing code

This is a **preview** product and the API surface moves. Your training data may be stale or
invented. **Before generating non-trivial Rayfin code, verify decorator names, client method
signatures, and CLI flags against the live docs** using the `microsoft_docs_search` /
`microsoft_docs_fetch` MCP tools (or `microsoft-docs` skill) and the reference files in this
skill. Inventing a decorator or a `RayfinClient` method that doesn't exist is the most common
failure mode here — a quick lookup prevents it.

If the user hasn't confirmed prerequisites, remind them once: the **workspace needs Fabric
capacity**, and a **tenant admin must enable the "Fabric Apps (preview)" workload** before any
deploy will work.

## The core workflow

Follow these stages. Most requests are "do one stage" — identify which and go there.

### 1. Scaffold or initialize

New project from a template:

```bash
npm create @microsoft/rayfin@latest my-app   # add --workspace <name> to target a workspace
cd my-app
```

Add Rayfin to an existing/empty directory instead (interactive: pick services, dialect, hosting):

```bash
npx rayfin init .
# non-interactive example:
npx rayfin init my-app --services db,storage --auth-methods fabric --static-hosting --overwrite
```

This produces the standard layout (see `references/cli-and-config.md` for the full tree):

```
your-project/
├── rayfin/
│   ├── data/          # entity classes + schema.ts  ← you spend most time here
│   ├── rayfin.yml     # backend service config (auth/data/storage/staticHosting)
│   ├── .env           # local secrets/values for interpolation (gitignore it)
│   └── tsconfig.json  # generated; don't edit
├── src/               # frontend (React/Vue/… depending on template)
└── package.json
```

### 2. Define data models

Each entity is a class in `rayfin/data/*.ts`. This is the heart of the platform — get the
decorators right and everything downstream (DB, API, types) follows. **Read
`references/data-modeling.md` for the full decorator reference, relationships, and the gotchas;**
the essentials:

```typescript
import { entity, uuid, text, int, decimal, boolean, date, set, one, many } from '@microsoft/rayfin-core';

@entity()
export class Notebook {
  @uuid() id!: string;
  @text({ min: 1, max: 100 }) name!: string;
  @date() createdAt!: Date;
  @many(() => Note) notes?: Note[];        // one-to-many: @many on parent
}

@entity()
export class Note {
  @uuid() id!: string;
  @text() title!: string;
  @text({ optional: true }) content?: string;
  @boolean({ default: false }) isPinned!: boolean;
  @date() createdAt!: Date;
  @one(() => Notebook) notebook?: Notebook;  // many-to-one: @one on child, auto-creates notebook_id FK
}
```

Three rules that bite people constantly (full list in the reference):

- **`@entity()` ≠ TypeScript optional.** A trailing `?` only changes the static type. To make a
  **column nullable you must pass `{ optional: true }`** to the decorator. Use `!` to tell
  TypeScript the framework initializes required fields.
- **Register every entity in `rayfin/data/schema.ts`** or the client can't see it.
- **Use relative imports with `.js` extensions** between entity files (`from './Note.js'`) — the
  emitted ESM needs them.

### 3. Add authorization (`@role`)

Permissions live next to the data, as class-level `@role(roleName, actions, options?)` decorators.
The only built-in role is `authenticated`. Policies are type-safe expressions over JWT `claims`
and the `item`. Owner-only is the workhorse pattern:

```typescript
@entity()
@role('authenticated', '*', {
  policy: (claims, item) => claims.sub.eq(item.user_id),
})
export class Task {
  @uuid() id!: string;
  @text() title!: string;
  @text() user_id!: string;   // populate from claims.sub — no @one() to the system USER entity
}
```

See `references/data-modeling.md` for field-level `include`/`exclude`, per-action rules,
`.and()`/`.or()`, and admin-override patterns.

### 4. Use the data from the frontend

The generated `RayfinClient` gives a fluent, type-safe API — no raw GraphQL. **Read
`references/client-api.md` for the full CRUD/query/pagination/auth surface;** the shape:

```typescript
import { RayfinClient } from '@microsoft/rayfin-client';
import type { Note } from '../rayfin/data/Note';

type AppSchema = { Note: Note };

const client = new RayfinClient<AppSchema>({
  baseUrl: import.meta.env.VITE_RAYFIN_API_URL ?? 'http://localhost:5168',
  publishableKey: import.meta.env.VITE_RAYFIN_PUBLISHABLE_KEY,
});

const pinned = await client.data.Note
  .select(['id', 'title', 'notebook.name'])
  .where({ isPinned: { eq: true } })
  .orderBy({ createdAt: 'desc' })
  .execute();

await client.data.Note.create({ title: 'Standup', isPinned: false, createdAt: new Date() });
await client.data.Note.update({ id }, { title: 'Renamed' });
await client.data.Note.delete({ id });
```

Note the limitations you'll hit: no `count()` (use `results.length`), `PagedResult.totalCount`
isn't populated, select only the fields you need.

### 5. Run locally, then deploy

```bash
npm run dev          # frontend dev server (Vite-based templates)
npx rayfin up        # deploy to Fabric: creates the item, applies schema, builds + uploads static content
npx rayfin up --dry-run --verbose   # preview without changing anything
npx rayfin up db apply              # apply just schema changes (add --force for destructive ops — data loss risk)
```

`rayfin up` requires sign-in (it'll prompt) and **Fabric auth enabled** in `rayfin.yml`
(`services.auth.fabric.enabled: true`). Email/password works locally but **never** on a deployed
app. Full command/flag reference and `rayfin.yml` schema are in `references/cli-and-config.md`.

## Schema changes & caching gotcha

After editing entities, run `npx rayfin up db apply`. If the frontend keeps returning stale data
shapes after a schema change, the generated config under `rayfin/.temp/` is cached — stop the dev
stack, `rm -rf rayfin/.temp/`, and restart. Never commit `.temp/`.

## Security reminders to surface

- Static content is served from a **public URL** — keep secrets, API keys, and sensitive data out
  of frontend code and the repo. Use the publishable key (it's meant to be public); never embed
  real secrets.
- Sharing a deployed app needs the **Run and interact** item permission; deploying/editing needs
  **Edit (Write)**. Workspace roles don't override item-level permissions.

## Reference files

Load these as needed — they hold the depth that doesn't belong in this overview:

- `references/data-modeling.md` — every field decorator, type modifiers, relationships, foreign
  keys, the full `@role`/policy/field-permission system, and modeling gotchas.
- `references/client-api.md` — `RayfinClient` init, read/filter/sort/relationship-navigation,
  pagination, create/update/delete (incl. relationships), auth, and current limitations.
- `references/cli-and-config.md` — full Rayfin CLI command + flag reference, the `rayfin.yml`
  service schema, project structure, env vars, and the deployment sequence.

---
> Source: [NikolajUnserRichter/fabric-apps-skill](https://github.com/NikolajUnserRichter/fabric-apps-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
