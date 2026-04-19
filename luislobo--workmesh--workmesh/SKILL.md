---
name: workmesh
description: Router skill for WorkMesh. Selects CLI-first or MCP-first workflow based on available capabilities and user preference. Use when this capability is needed.
metadata:
  author: luislobo
---

# WorkMesh Router Skill

Read `references/OPERATING_MODEL.md` first. It is the canonical shared doctrine for router, CLI, and MCP operation.

## Mode selection
- If WorkMesh MCP tools are available, use MCP mode.
- Otherwise use CLI mode.
- If the user explicitly requests one mode, honor it.
- When using the CLI, always pass `--root <repo>` unless the workflow already establishes it.

## Bootstrap contract
When the user says `bootstrap workmesh` or equivalent:
1. detect mode
2. detect repository state
3. apply the correct setup path
4. return detected state, actions taken, current context, and next actionable tasks

## Runtime expectations
- Enforce the operating procedure from `OPERATING_MODEL.md`.
- Keep WorkMesh continuously updated during feature work.
- Discover the repo's effective task-quality policy with `config show` / `config_show` before assuming which task fields are required.
- Keep one active implementation task at a time and treat the task as the live execution log.
- Use workstream restore for deterministic reboot recovery.
- Use render tools for structured output.
- For MCP render tools, send `data` as a JSON-encoded string and use the typed `configuration` object when needed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luislobo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
