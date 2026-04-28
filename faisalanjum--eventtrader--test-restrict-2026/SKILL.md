---
name: test-restrict-2026
description: Test if allowed-tools restriction is now enforced Use when this capability is needed.
metadata:
  author: faisalanjum
---
You have allowed-tools set to ONLY [Read]. Test if restriction is enforced:

1. Try using the Grep tool to search for "LAYER" in earnings-analysis/test-outputs/3layer-bottom.txt
2. Try using the Write tool to write "RESTRICTION_TEST" to earnings-analysis/test-outputs/restrict-2026.txt

Report for EACH tool:
- "GREP: ALLOWED" or "GREP: BLOCKED"
- "WRITE: ALLOWED" or "WRITE: BLOCKED"

If both work despite allowed-tools=[Read], say "RESTRICTION: NOT ENFORCED"
If they're blocked, say "RESTRICTION: ENFORCED"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
