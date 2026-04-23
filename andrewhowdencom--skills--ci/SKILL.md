---
name: ci
description: Guidelines for GitHub Actions. Use when this capability is needed.
metadata:
  author: andrewhowdencom
---

# CI

## GitHub Actions
Use GitHub Actions for CI related tasks. They should typically invoke the [Taskfile](../taskfile/SKILL.md) to run standard build and test steps:

```bash
# Example step in .github/workflows/ci.yml
- name: Validate
  run: task validate
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrewhowdencom) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
