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
> Source: [trolzie/claude-loop-systems-demo](https://github.com/trolzie/claude-loop-systems-demo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-06 -->
