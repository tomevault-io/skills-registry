---
name: kubit-validating-design
description: Use after any change to skeleton/, packages/core/, or docs/. Use to verify types compile and documentation is synchronized. Use when this capability is needed.
metadata:
  author: aniftyco
---

# Validating Kubit Design Changes

## Overview

After any change, verify types compile and docs are synchronized.

## When to Use

- After modifying skeleton/ files
- After changing packages/core/ types
- After updating docs/
- Before considering a task complete

## Validation Checklist

```
After ANY change:
[ ] tsc --noEmit passes in skeleton/
[ ] SPEC.md updated if API changed
[ ] SKELETON_APP.md updated if structure changed
[ ] CLAUDE.md updated if docs/ changed
```

## Type Validation

Run from skeleton/ directory:
```bash
cd skeleton && npx tsc --noEmit
```

**Common failures:**
- Missing type in packages/core/ for new skeleton usage
- Import path typo
- Decorator usage not matching type signature

## Documentation Sync

| If you changed... | Also update... |
|-------------------|----------------|
| skeleton/ structure | docs/SKELETON_APP.md |
| API in packages/core/ | docs/SPEC.md |
| Any file in docs/ | .claude/CLAUDE.md |
| Test expectations | docs/TEST_PLAN.md |

## Quick Verification Commands

```bash
# Type check
cd skeleton && npx tsc --noEmit

# List skeleton structure (compare to SKELETON_APP.md)
find skeleton -type f -name "*.ts" -o -name "*.tsx" | sort
```

## Red Flags

- [ ] Added to skeleton without adding types → tsc will fail
- [ ] Changed API without updating SPEC → spec drift
- [ ] Modified docs/ without updating CLAUDE.md → agent confusion
- [ ] New pattern not in SKELETON_APP.md → incomplete documentation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aniftyco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
