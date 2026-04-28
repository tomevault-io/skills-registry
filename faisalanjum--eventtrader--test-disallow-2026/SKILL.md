---
name: test-disallow-2026
description: Test if disallowedTools blocks tools Use when this capability is needed.
metadata:
  author: faisalanjum
---
You have disallowedTools set to [Write, Bash]. Test if blocking works:

1. Try using Write to create earnings-analysis/test-outputs/disallow-2026.txt with content "DISALLOW_TEST"
2. Try using Bash to run "echo hello"

Report for EACH:
- "WRITE: BLOCKED" or "WRITE: ALLOWED"
- "BASH: BLOCKED" or "BASH: ALLOWED"

If both work despite disallowedTools, say "DISALLOWED_TOOLS: NOT ENFORCED"
If they're blocked, say "DISALLOWED_TOOLS: ENFORCED"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
