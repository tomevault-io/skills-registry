---
name: compile
description: Compile the Go shared library for local development Use when this capability is needed.
metadata:
  author: joelmoss
---

# Compile Go Binary

Compile the Go shared library needed for Ruby tests and development.

## Steps

1. Run `bundle exec rake compile:local`
2. Verify the binary exists at `lib/proscenium/ext/proscenium`
3. Report success or failure

---
> Source: [joelmoss/proscenium](https://github.com/joelmoss/proscenium) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
