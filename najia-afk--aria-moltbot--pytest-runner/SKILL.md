---
name: aria-pytest
description: Run pytest for the Aria codebase and return structured results. Use when this capability is needed.
metadata:
  author: najia-afk
---

# aria-pytest

Run pytest in the aria workspace and return stdout/stderr with exit codes.

## Usage

```bash
exec python3 /app/skills/run_skill.py pytest run_pytest '{"paths": ["tests"], "markers": "not slow"}'
```

## Functions

### run_pytest
Run pytest for the requested paths.

```bash
exec python3 /app/skills/run_skill.py pytest run_pytest '{"paths": ["tests"], "extra_args": ["-q"]}'
```

### collect_pytest
Collect tests without executing them.

```bash
exec python3 /app/skills/run_skill.py pytest collect_pytest '{"paths": ["tests"], "markers": "integration"}'
```

## Environment

- `PYTEST_WORKSPACE` (default: /app/workspace)
- `PYTEST_TIMEOUT_SEC` (default: 600)
- `PYTEST_DEFAULT_ARGS` (default: -q)

## Python Module

This skill wraps `/app/skills/aria_skills/pytest_runner.py`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/najia-afk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
