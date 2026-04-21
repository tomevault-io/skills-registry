---
name: bronze-demo-check
description: > Use when this capability is needed.
metadata:
  author: sofia-asif
---

# Bronze Demo Check

Run a repeatable end-to-end check:
1) vault exists and is writable
2) watcher creates `Needs_Action` items
3) Claude processes and writes back plans/dashboard updates
4) items get archived to `Done/`

## Checklist

Follow `references/bronze-acceptance.md`.

## Outputs to show in the demo

- Vault tree showing required files/folders
- A newly created `Needs_Action/*.md`
- A generated plan file
- An updated `Dashboard.md`
- The original item moved to `Done/`

## Resources

- Reference: `references/bronze-acceptance.md`
- Example demo script (human-readable): `templates/DemoRun.md`
- Examples: `examples.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sofia-asif) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
