---
name: minion-sweep
description: > Use when this capability is needed.
metadata:
  author: felixmanojh
---

# Minion Sweep

Codebase-wide maintenance scan.

## When to Invoke

- User requests "add docstrings to all files"
- Periodic codebase cleanup
- Tech debt reduction

## Commands

```bash
# Discover what needs work
source .venv/bin/activate && python scripts/minions.py --json sweep <dir> --task <task>

# Apply fixes
source .venv/bin/activate && python scripts/minions.py --json sweep <dir> --task <task> --apply
```

## Tasks

| Task | What it checks |
|------|----------------|
| `all` | Everything (default) |
| `docstrings` | Missing function/class/module docstrings |
| `types` | Missing type hints |
| `headers` | Missing module-level docstrings only |

## Examples

```bash
# Discover
python scripts/minions.py --json sweep src/ --task docstrings

# Apply
python scripts/minions.py --json sweep src/ --task docstrings --apply

# With backups
python scripts/minions.py --json sweep src/ --task all --apply --backup
```

## Output (Discover)

```json
{
  "candidates": [
    {"file": "src/foo.py", "lines": 120, "missing": ["3 functions without docstrings"]}
  ],
  "total_candidates": 1,
  "skipped": []
}
```

## Claude Integration

1. Run discover first to show scope
2. Confirm with user: "Found 12 files. Apply?"
3. Run with --apply on approval
4. Report summary

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/felixmanojh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
