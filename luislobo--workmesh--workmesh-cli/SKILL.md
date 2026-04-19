---
name: workmesh-cli
description: CLI-first WorkMesh workflow. Use when agents should run shell commands instead of MCP tool calls. Use when this capability is needed.
metadata:
  author: luislobo
---

# WorkMesh CLI Skill

Read `references/OPERATING_MODEL.md` first. It is the canonical shared doctrine for router, CLI, and MCP operation.

## CLI mode rules
- Always pass `--root <repo>`.
- Prefer `--json` when parsing output.
- Read `config show --json` when task-quality policy or storage roots might matter.
- Keep one active implementation task at a time and update the task notes before/during coding.
- Use `workmesh --root <repo> render ...` before hand-formatting structured output.

## Bootstrap contract
Use `doctor`, `quickstart`, `migrate`, `context show`, `truth list`, and `next` according to repo state.

## CLI helpers
- `workmesh --root . workstream restore --json`
- `workmesh --root . workstream show <id-or-key> --restore --json`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luislobo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
