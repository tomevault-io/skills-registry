---
name: check
description: Load `dev/checking.md` for detailed guidance on fixing errors. Use when this capability is needed.
metadata:
  author: lemeb
---

# Linting and Type-Checking

Load `dev/checking.md` for detailed guidance on fixing errors.

## Procedure

1. Run `make check-fix` (auto-fixes formatting and simple issues)
2. Run `make check` - must pass
3. Run `make check-strict-all` - must pass
4. If errors occur, fix them using guidance from `dev/checking.md`
5. Repeat until both check commands pass

## Output Format

```text
## Linting & Type-Checking Results

- `make check`: PASS/FAIL
- `make check-strict-all`: PASS/FAIL
- Errors fixed: <count and brief list>

### Lessons Learned
<Patterns discovered while fixing issues — things future agents should know>
<If none, write "None">
```

**Always include Lessons Learned**, even if empty. When running as a sub-agent,
the main agent needs this for the PR description.

Examples of lessons learned:

- "Discovered that `Optional[X]` is required for all nullable parameters with
  --strict"
- "Class variables need explicit type annotations even when assigned
  immediately"
- "Import ordering: stdlib → third-party → local, with blank lines between
  groups"

## Exit Conditions

- **Success**: Both `make check` and `make check-strict-all` pass
- **Failure**: Report remaining errors clearly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lemeb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
