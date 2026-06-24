---
name: mayrlabs-internal-php-laravel
description: Extracted PHP / Laravel standards from MayR Labs GEMINI.md Use when this capability is needed.
metadata:
  author: MayR-Labs
---

# MayR Labs Internal: PHP (Laravel) Standards

- Prefer **imported classes over FQNs**
- Controllers must be thin:
  - Move logic to **Services / Actions**

## Validation

- Use Form Requests — no inline validation mess

---
> Source: [MayR-Labs/skills](https://github.com/MayR-Labs/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
