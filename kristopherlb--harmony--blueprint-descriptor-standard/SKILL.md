---
name: blueprint-descriptor-standard
description: Apply BDS-001 to define and audit the TypeScript/Node blueprint descriptor contract used for registry discovery and MCP manifest generation. Use when this capability is needed.
metadata:
  author: kristopherlb
---

## Blueprint Descriptor Standard (BDS-001)

Use this skill when adding or updating blueprint descriptors so they remain rich enough for MCP exposure and audits without bundling workflow code.

### When to Use

- Creating a new blueprint descriptor under `packages/blueprints/src/descriptors/`
- Updating manifest generation that reads blueprint descriptors
- Auditing blueprint governance fields (owner, costCenter, roles, SLA, classification)

### Instructions

1. **Keep IDs consistent**: `blueprintId` must equal `metadata.id` and registry key.
2. **Include governance fields**: metadata/security/operations are required.
3. **Include schemas**: input + config always; output is required for MCP-exposed tools.
4. **Validate aiHints**: exampleInput must validate against input; for MCP tools, exampleOutput must validate against output.
5. **Use CSS-001** classification semantics (do not restate).

See `references/blueprint-descriptor-standard.md` for the normative specification.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kristopherlb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
