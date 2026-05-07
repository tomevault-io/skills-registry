---
name: test-blocked-fixture
description: > Use when this capability is needed.
metadata:
  author: neversight
---

# Test Blocked Fixture

This skill exists solely for testing the skill-auditor. It should always
report as BLOCKED with the following violations:

## Expected Violations

- **B5**: Description contains angle brackets (< or >)
- **W2**: Unexpected frontmatter property: extra_property

## Usage

```bash
# Should exit 1 with BLOCKED status
uv run skill-auditor skills/test-blocked-fixture

# Hybrid mode should show structured blockers
uv run skill-auditor --hybrid skills/test-blocked-fixture
```

Do not fix these violations - they are intentional.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
