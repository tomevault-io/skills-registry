---
name: prepare-commit
description: Prepares the codebase for a commit by formatting code and helping identify temporary comments. Use when this capability is needed.
metadata:
  author: google
---

# Prepare Commit

Use this skill **before every commit** to ensure your code is formatted and clean.

## Usage

This skill performs the following:
1.  Runs standard formatting (`ktfmtFormat`).
2.  (Optional/Manual) Reminds you to check for temporary comments.

### Command

```bash
python3 .agent/skills/prepare_commit/scripts/prepare.py
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/google) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
