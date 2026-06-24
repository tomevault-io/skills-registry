---
name: fix-clippy
description: Fix all clippy lint warnings in the project Use when this capability is needed.
metadata:
  author: quickwit-oss
---

# Fix Clippy

Clippy issues are **warnings**, not errors. Never grep for `error` when looking for clippy issues.

## Step 1: Auto-fix

Run `make fix` to automatically fix clippy warnings:

```
make fix
```

## Step 2: Fix remaining warnings manually

Check for remaining warnings that couldn't be auto-fixed:

```
cargo clippy --tests 2>&1 | grep "^warning:" | sort -u
```

For each remaining warning, find the exact location and fix it manually.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/quickwit-oss) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
