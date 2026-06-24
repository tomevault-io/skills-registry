---
name: ai-rulez
description: End-to-end checklist for delivering new capabilities across CLI, presets, and tests Use when this capability is needed.
metadata:
  author: Goldziher
---

# Capability Delivery Checklist

- Capture an implementation plan and accompanying tests before touching code.
- Update docs, schema, and multi-runtime wrappers in the same change set.
- Include fixture or validation coverage that demonstrates the new feature.
- Run `go test ./...`, `ai-rulez validate`, and regenerate outputs before opening a PR.

---
> Source: [Goldziher/ai-rulez](https://github.com/Goldziher/ai-rulez) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-18 -->
