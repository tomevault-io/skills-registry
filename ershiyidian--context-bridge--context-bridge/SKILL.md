---
name: context-bridge
description: Pack bounded repository context, apply SemPatch YAML safely, and use the MCP/CLI/browser companion workflow for local codebases. Use when this capability is needed.
metadata:
  author: ershiyidian
---

# Context Bridge

Use this skill when a task needs local repository context packaged for a web LLM, MCP client,
browser companion, or another agent that can consume files and structured patches.

- Use `bundle`, `map`, `select`, or `playbook` to stage context.
- Instruct the model to return only `--- sempatch ---` YAML blocks.
- Use `apply` in dry-run mode first, then `--write` only after the diff is understood.
- Prefer MCP when the client can call tools directly.
- Use the browser companion for web UI workflows that would otherwise rely on manual copy/paste.

## Core Commands

```bash
context-bridge bundle <repo> --manifest-only -o manifest.md
context-bridge map <repo> --query "bug in login flow" -o repo-map.md
context-bridge playbook <repo> --task "bug in login flow" -o playbook.md
context-bridge select <repo> --task "bug in login flow" -o selected-context.md
context-bridge apply answer.md --root <repo>
context-bridge apply answer.md --root <repo> --write
context-bridge migrate-old-patches legacy-answer.md -o answer.sempatch.md
context-bridge mcp
```

## Packing Strategy

Start narrow, then widen:

1. Generate a manifest first when you do not know the repository shape.
2. Generate a repo map when the task mentions symbols or ownership.
3. Use `select` for task-aware file choice within a token budget.
4. Use `bundle` when you already know the exact files or globs needed.
5. Add visual snapshots with `--visual` for frontend components.

## SemPatch

SemPatch blocks look like this:

```yaml
--- sempatch ---
file: src/example.py
language: python
op: replace
selector:
  rule:
    pattern: |
      def hello():
        $$$BODY
replacement: |
  def hello():
      return "updated"
--- end sempatch ---
```

For new files, use:

```yaml
--- sempatch ---
file: src/new_file.py
language: python
op: create_file
content: |
  print("hello")
--- end sempatch ---
```

Selectors must match exactly one AST node. If a node is ambiguous, add `selector.anchor`.

## MCP Surface

- `pack_context`
- `repo_map`
- `context_playbook`
- `select_context_for_task`
- `scan_text_for_secrets`
- `inspect_sempatches`
- `apply_patches`
- `sempatch_schema`

## Profiles

Store repeatable defaults in `.context-bridge.toml`.

```toml
[profiles.default]
include = ["src/**/*.py", "tests/**/*.py", "pyproject.toml"]
exclude = ["**/fixtures/**"]
extensions = ["py", "md", "toml"]
max_total_bytes = 1000000
respect_gitignore = true

[verify]
python = ["python", "-m", "ruff", "check", "--quiet"]
```

## References

- `references/workflow.md`
- `references/ecosystem.md`
- `references/patch-format.md`
- `references/competitive-landscape.md`

---
> Source: [ershiyidian/context-bridge](https://github.com/ershiyidian/context-bridge) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
