---
name: fmt
description: Run `make fmt` to check the code format. Use when this capability is needed.
metadata:
  author: quickwit-oss
---

# Format Check

Run `make fmt` from the `quickwit/` subdirectory to check code formatting:

```
cd /Users/paul.masurel/git/quickwit/quickwit && make fmt
```

This command checks:
1. Rust code formatting
2. License headers
3. Log format policy (no trailing punctuation, no uppercase first character)

If there are log format issues, fix them by:
- Making the first character lowercase
- Removing trailing punctuation (periods, exclamation marks, etc.)

Fix any issues found and re-run until clean.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/quickwit-oss) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
