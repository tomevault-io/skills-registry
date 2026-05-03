---
name: verify
description: Run the repo verification suite (lint + tests) and summarize results. Use when this capability is needed.
metadata:
  author: trolzie
---

# Verify

Run the verification suite and summarize results concisely.

## Command

```bash
npm run verify
```

## Output format

```md
✅ VERIFY PASS
- lint: PASS
- tests: PASS
```

Or:

```md
❌ VERIFY FAIL
- lint: FAIL
- tests: SKIPPED | FAIL

Next step: <most likely fix / where to look>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/trolzie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
