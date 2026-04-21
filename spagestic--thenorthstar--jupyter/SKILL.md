---
name: jupyter-notebook
description: Interact with Jupyter notebooks. Use this to "run cell", "read notebook", or "execute python in notebook". Use when this capability is needed.
metadata:
  author: spagestic
---

# Jupyter Skill

Uses `jupyter-mcp-server` to execute code in notebooks.

## Usage

**Command Template:**

```bash
npx mcporter call --command "uvx jupyter-mcp-server --transport=stdio" --tool <tool_name> --args <json_args>
```

## Common Tools

- `execute_cell`: Run a specific cell in a notebook.

- `read_notebook`: Read the contents of a .ipynb file.

Example: Read a notebook

```bash
npx mcporter call --command "uvx jupyter-mcp-server --transport=stdio" --tool read_notebook --args '{"path": "analysis.ipynb"}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spagestic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
