---
name: refs
description: Sync and search local reference repositories (curated awesome-* lists) to support planning/design decisions. Use when this capability is needed.
metadata:
  author: 45ck
---

# /sdlc:refs - Local Reference Repos (Awesome Lists)

Use this skill to keep curated `awesome-*` repos available locally so other SDLC skills and agents can `Read`/`Grep` them during planning and design.

## Sync / Update

```bash
${CLAUDE_PLUGIN_ROOT}/scripts/awesome/sync.sh
```

This also writes a lock file (commit SHAs per repo):
- `${CLAUDE_PLUGIN_ROOT}/references/awesome/LOCK.json`

## How To Search

```bash
rg -n "observability" "${CLAUDE_PLUGIN_ROOT}/references/awesome/repos"
rg -n "threat model" "${CLAUDE_PLUGIN_ROOT}/references/awesome/repos"
rg -n "architecture decision record|ADR" "${CLAUDE_PLUGIN_ROOT}/references/awesome/repos"
```

## Optional: Build/Search A Catalog

This builds a small SQLite catalog (FTS) from the local README files:

```bash
${CLAUDE_PLUGIN_ROOT}/scripts/awesome/catalog.py build
${CLAUDE_PLUGIN_ROOT}/scripts/awesome/catalog.py search "OpenTelemetry"
${CLAUDE_PLUGIN_ROOT}/scripts/awesome/catalog.py search "\"feature flags\"" --limit 10
${CLAUDE_PLUGIN_ROOT}/scripts/awesome/catalog.py search "OpenTelemetry" --no-social
```

## Where The Seed List Lives

- `${CLAUDE_PLUGIN_ROOT}/references/awesome/seeds.tsv`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/45ck) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
