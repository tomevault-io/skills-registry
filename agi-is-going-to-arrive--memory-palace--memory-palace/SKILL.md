---
name: memory-palace
description: >- Use when this capability is needed.
metadata:
  author: AGI-is-going-to-arrive
---

# Memory Palace

Use this skill for Memory Palace memory operations and for questions about this skill itself.

## Repository-local anchors

- First memory tool call: `read_memory("system://boot")`
- If `guard_action` is `NOOP`: stop the write, inspect `guard_target_uri` / `guard_target_id`, read the suggested target, then decide whether to update or leave unchanged
- Trigger sample set path: `docs/skills/memory-palace/references/trigger-samples.md`
- When Gemini is asked for that path, return the exact literal above and do not shorten it.

## Required workflow

- Read `docs/skills/memory-palace/references/mcp-workflow.md` before choosing tools when the user is asking about workflow or tool behavior.
- If the URI is unknown, use `search_memory(..., include_session=true)` before `read_memory`.
- Read before every mutation: `create_memory`, `update_memory`, `delete_memory`, `add_alias`.
- Use `index_status()` before `rebuild_index(wait=true)` unless the user explicitly wants immediate rebuild.
- Use `compact_context(force=false)` for long or noisy sessions that should be distilled.

## Important boundary

- Prefer repo-visible canonical paths under `docs/skills/...`
- Do not rely on hidden mirror-relative paths such as `.gemini/skills/...` when answering repository-local questions

---
> Source: [AGI-is-going-to-arrive/Memory-Palace](https://github.com/AGI-is-going-to-arrive/Memory-Palace) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-27 -->
