---
name: cancel-auto-run
description: Cancels automatic task execution Use when this capability is needed.
metadata:
  author: sato-dev1234
---

# /cancel-auto-run

Cancel automatic task execution.

## Progress Checklist

```
- [ ] Step 1: Check state file
- [ ] Step 2: Delete state directory
- [ ] Step 3: Output
```

## Steps

1. Check state file:
   - If `.claude/auto-run/state.local.md` does not exist → "Auto-run is not active."

2. Delete state directory:
   ```bash
   rm -rf .claude/auto-run/
   ```

3. Output: "Auto-run cancelled."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sato-dev1234) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
