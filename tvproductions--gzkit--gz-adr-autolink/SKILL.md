---
name: gz-adr-autolink
description: Maintain ADR verification links by scanning @covers decorators and updating docs. Use when this capability is needed.
metadata:
  author: tvproductions
---

# gz-adr-autolink

Maintain ADR-to-test linkage using current repository workflows.

## Procedure

```bash
# 1) Discover coverage annotations
rg -n '@covers\("ADR-' tests

# 2) Validate linkage for target ADR
uv run gz adr audit-check ADR-0.3.0 --json

# 3) Re-lint after doc updates
uv run gz lint
```

## Notes

- There is no dedicated `gz adr autolink` command in this repository.
- Update ADR verification sections manually based on discovered coverage.

## References

- Command implementation: `src/gzkit/cli.py`
- Related docs: `docs/user/commands/adr-audit-check.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tvproductions) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
