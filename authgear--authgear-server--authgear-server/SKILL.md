---
name: add-portal-admin-api-mutation
description: Add a Portal Admin GraphQL mutation under portal/src/graphql/adminapi/mutations. Use when this capability is needed.
metadata:
  author: authgear
---

Follow this skill when adding a Portal Admin GraphQL mutation.

## Workflow

1. Read `./portal/src/graphql/adminapi/schema.graphql` first.
2. Create the `.graphql` file under `./portal/src/graphql/adminapi/mutations`.
3. Query all fields on the top-level entity unless the user explicitly says otherwise.
4. Avoid nested entities unless the user asked for them.
5. Run `cd ./portal && npm run gentype` after adding the file.

## Notes

- Keep the operation name consistent with existing Portal GraphQL naming.
- Do not invent schema fields; use only what exists in the schema file.

---
> Source: [authgear/authgear-server](https://github.com/authgear/authgear-server) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
