---
name: python-executor
description: Execute Python scripts and snippets. Use this for "run python script", "calculate with python", or "test snippet". Use when this capability is needed.
metadata:
  author: spagestic
---

# Python Executor Skill

Uses `mcp-run-python` to execute Python code safely.

## Usage

**Command Template:**

```bash
npx mcporter call --command "uvx mcp-run-python" --tool <tool_name> --args <json_args>
```

## Common Tools

- `run_python`: Execute a python script or code block.

Example: Run a calculation

```bash
npx mcporter call --command "uvx mcp-run-python" --tool run_python --args '{"code": "print(25 * 4)"}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spagestic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
