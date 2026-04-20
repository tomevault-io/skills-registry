---
name: minion-polish
description: > Use when this capability is needed.
metadata:
  author: felixmanojh
---

# Minion Polish

Dispatch local model to add mechanical polish. Changes are auto-applied.

## When to Invoke

- After implementing a feature
- Files need docstrings, type hints, cleanup
- Mechanical changes only (no logic changes)

## Command

```bash
source .venv/bin/activate && python scripts/minions.py --json polish <files> --task <task>
```

## Tasks

| Task | Description |
|------|-------------|
| `all` | Docstrings + types + headers (default) |
| `docstrings` | Add function/class docstrings |
| `types` | Add type hints |
| `headers` | Add module-level docstrings |

## Examples

```bash
# Polish single file
python scripts/minions.py --json polish src/foo.py --task all

# Multiple files
python scripts/minions.py --json polish src/foo.py src/bar.py --task docstrings

# Dry run
python scripts/minions.py --json polish src/foo.py --task all --dry-run
```

## Output

```json
{
  "applied": true,
  "files_modified": ["src/foo.py"],
  "changes": ["src/foo.py: Added 3 docstring(s)"],
  "errors": [],
  "stats": {"total": 1, "applied": 1, "failed": 0}
}
```

## Claude Integration

After completing code:
- Small (1-2 files): Auto-invoke, report summary
- Medium (3+ files): Ask user "Want me to dispatch minions for polish?"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/felixmanojh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
