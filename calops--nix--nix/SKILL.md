---
name: python-dev
description: Python guidelines. Use when writing, reviewing or specifying python code Use when this capability is needed.
metadata:
  author: calops
---

# Be idiomatic

Put a special importance on being *idiomatic*, in particular *relative to the current python version used*.
Don't rely on old best practices and idioms, use modern constructs when relevant. For example:

- Don't use the "lambdas in dicts" dispatching pattern, use pattern matching instead when available.
- Don't use juxtaposition of strings for multiline strings, use triple quotes instead.

# Correctness

Always strive to be correct. That means using the standard, idiomatic constructs of the language and
frameworks you're using. Don't take shortcuts by disabling lints with #noqa.

---
> Source: [calops/nix](https://github.com/calops/nix) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-29 -->
