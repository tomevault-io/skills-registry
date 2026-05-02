---
name: static-checks
description: checks to run on code Use when this capability is needed.
metadata:
  author: codingitwrong
---

# Overview

After any change to the codebase, run the following static checks:

- Run the full rspec test suite with `bin/rspec` and fix any failures. If you think you need to change the tests, think hard about whether that is correct or whether there is a bug in the application functionality.
- Fix format issues with `standardrb --fix`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codingitwrong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
