---
name: openwebf-release
description: DEPRECATED umbrella Skill (backward compatibility). Use only when release work spans deployment + cache headers + versioning + rollback. Prefer focused `openwebf-release-cdn-deploy` and `openwebf-release-versioning-rollback` Skills. Use when this capability is needed.
metadata:
  author: archview-ai
---

# OpenWebF Release Skill

## Deprecated

Prefer:
- `openwebf-release-cdn-deploy`
- `openwebf-release-versioning-rollback`

## Instructions

If this umbrella Skill is active, route to the appropriate focused release Skill based on trigger terms (CDN/provider vs versioning/rollback/rollout). Ensure store compliance when remote updates are involved.

If repo context is missing, start with `/webf:doctor` and then route to the focused Skill.

## Examples

- “Design a rollout and rollback plan for CDN-hosted WebF assets with cache-busting.”

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/archview-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
