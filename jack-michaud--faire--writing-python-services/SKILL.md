---
name: writing-python-services
description: Writing a class with encapsulated logic that interfaces with an external system. Logging, APIs, etc. Use when this capability is needed.
metadata:
  author: jack-michaud
---

- Use python 3.12+ syntax for types
  - e.g. `|` for unions, ` | None` for optional

- No side effects in constructor
  - Use `@cached_property` and lazily evaluate properties needing IO

- Methods use `dataclasses` for arguments and responses

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jack-michaud) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
