---
name: marketing
description: | Use when this capability is needed.
metadata:
  author: cacheplane
---

# Marketing Cowork skill — STUB

Implementation lands in the cowork-loop sub-spec
(`docs/superpowers/specs/marketing/<date>-cowork-loop-design.md`).

This file exists so the directory shape and skill name are reserved.
Do NOT invoke this skill until the cowork-loop sub-spec is merged.

## Expected file conventions (preview)

- `marketing/cowork/inbox/<id>.json` — drafts awaiting review (agent writes)
- `marketing/cowork/outbox/<id>.json` — approved + posted (skill writes)
- `marketing/cowork/archive/<id>.json` — rejected or expired (skill writes)

`<id>` is `YYYY-MM-DD-<short-slug>`.

## Expected DraftBundle shape (preview)

See `docs/superpowers/specs/marketing/2026-05-17-marketing-meta-design.md` §5.3.

---
> Source: [cacheplane/angular-agent-framework](https://github.com/cacheplane/angular-agent-framework) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-06 -->
