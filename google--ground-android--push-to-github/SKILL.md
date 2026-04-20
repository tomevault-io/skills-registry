---
name: push-to-github
description: Runs all necessary checks (lint, tests) and pushes to GitHub. Use this as the final safety gate.
metadata:
  author: google
---

# Push to GitHub

Use this skill **when you are ready to push** your changes to the remote repository. It acts as a safety gate to prevent breaking CI.

## Usage

1.  Verifies changes (Unit Tests).
2.  Runs code checks (Lint/CheckCode).
3.  Pushes to the current branch.

### Command

```bash
python3 .agent/skills/push_to_github/scripts/push.py
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/google) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
