---
name: maya-dev
description: |- Use when this capability is needed.
metadata:
  author: loonghao
---
# maya-dev (Pipeline stage)

Development workflow helpers for agents working on Maya Python tools in a
live Maya session. The intended loop is:

1. `attach_project` once for the project root.
2. Edit files in the normal workspace.
3. `run_check` or `reload_modules` + `run_entrypoint`.
4. Inspect the concise summary, artifact refs, session events, and optional UI screenshot.

Set `DCC_MCP_MAYA_DEV_ROOTS` to a path-list of trusted roots when a studio
wants to restrict which local projects can be attached.

## Scripts

- `attach_project` — Add a development project root to Maya's `sys.path`
- `reload_modules` — Purge or reload modules belonging to the attached project
- `run_entrypoint` — Import and call a Python entrypoint from the project
- `run_script` — Run a project-local `.py` script with captured output
- `start_debugpy` — Start a debugpy listener in the Maya process with attach metadata
- `capture_ui` — Capture the Maya main window or a named Qt widget as PNG
- `ui_snapshot` / `ui_find` / `ui_action` — Inspect and operate Maya Qt UI controls, with optional action evidence artifacts
- `make_node_ref` / `resolve_node_ref` — Build and resolve stable Maya node references
- `run_check` — Reload, run an entrypoint, capture diagnostics/artifacts/session events, optionally grab UI

---
> Source: [loonghao/dcc-mcp-maya](https://github.com/loonghao/dcc-mcp-maya) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
