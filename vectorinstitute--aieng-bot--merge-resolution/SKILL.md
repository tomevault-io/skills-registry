---
name: merge-resolution
description: Merge conflict resolution conventions. Use when resolving git merge conflicts, especially in dependency files and lock files. Use when this capability is needed.
metadata:
  author: vectorinstitute
---

# Merge Resolution

## Version Conflicts

Prefer **newer versions**:
```
<<<<<<< HEAD
"package": "^2.0.0"
=======
"package": "^1.9.0"
>>>>>>>
# Resolve to: "^2.0.0"
```

## Lock Files

Never manually resolve. Regenerate:
```bash
# Python
rm uv.lock && uv lock

# Node.js
rm package-lock.json && npm install
```

## Pre-commit Config

For `.pre-commit-config.yaml`, prefer **newer hook versions** from the update branch.

## Validation

```bash
git diff --check           # No whitespace issues
grep -r "<<<<<<" .         # No remaining markers
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vectorinstitute) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
