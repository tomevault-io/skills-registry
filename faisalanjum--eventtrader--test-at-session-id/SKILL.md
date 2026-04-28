---
name: test-at-session-id
description: Test ${CLAUDE_SESSION_ID} substitution in skill content Use when this capability is needed.
metadata:
  author: faisalanjum
---

# Session ID Substitution Test

The session ID for this execution is: "${CLAUDE_SESSION_ID}"

Write the following to `earnings-analysis/test-outputs/test-at-session-id.txt`:

```
SESSION_ID: ${CLAUDE_SESSION_ID}
IS_EMPTY: <true if the value above is literally "${CLAUDE_SESSION_ID}" or empty, false if it's a real ID>
ID_LENGTH: <character count of the session ID>
TIMESTAMP: <current ISO timestamp>
```

Then return "SESSION_ID_TEST_COMPLETE".

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
