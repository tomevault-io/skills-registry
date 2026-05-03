---
name: python-venv
description: Create and run Python scripts in a local virtual environment with reproducible command patterns. Use when this capability is needed.
metadata:
  author: zliu95
---

# Python venv

Use this skill when the task requires writing and running Python code with isolated dependencies.

## Core rule

`exec` calls are stateless across invocations. Do not rely on `source .venv/bin/activate` persisting between calls.
Always call the interpreter and pip directly from the venv path.

## Standard workflow

1. Create a project-local environment:
```bash
python3 -m venv .venv
```

2. Install dependencies:
```bash
.venv/bin/python -m pip install --upgrade pip
.venv/bin/python -m pip install -r requirements.txt
```

3. Run scripts:
```bash
.venv/bin/python script.py
```

## Recommended execution pattern

When possible, run setup + execution in one command so context is explicit:

```bash
python3 -m venv .venv && \
.venv/bin/python -m pip install --upgrade pip && \
.venv/bin/python -m pip install -r requirements.txt && \
.venv/bin/python script.py
```

## File layout

- Keep scripts in workspace-local paths, for example `scripts/` or repo root.
- Keep environment in `.venv/` at project root.
- Keep dependency pins in `requirements.txt`.

## Troubleshooting

- `python3: command not found`: install Python 3 and rerun.
- `No module named pip`: recreate venv with `python3 -m venv .venv --upgrade-deps` if available.
- Build tool errors on native packages: report the missing system dependency and stop before retry loops.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zliu95) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
