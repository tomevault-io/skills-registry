---
name: run-with-timeout
description: name: run-with-timeout Use when this capability is needed.
metadata:
  author: manuelkugelmann
---
---
name: run-with-timeout
description: Run commands with timeout protection to prevent hanging. Use for potentially long-running tests or operations.
---

# Run With Timeout

Execute commands with timeout protection.

```bash
.claude/skills/run-with-timeout/scripts/run-with-timeout.sh 60 ./test-script.sh
```

Returns exit code 124 if timeout exceeded, otherwise command's exit code.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/manuelkugelmann) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
