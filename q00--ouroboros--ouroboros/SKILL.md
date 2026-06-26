---
name: run
description: This fixture keeps dispatch metadata in YAML frontmatter while the markdown Use when this capability is needed.
metadata:
  author: Q00
---
# Run

This fixture keeps dispatch metadata in YAML frontmatter while the markdown
body contains text that looks similar enough to catch accidental whole-file
parsing.

```yaml
mcp_tool: body_should_not_be_loaded
mcp_args:
  seed_path: body-value
  cwd: body-cwd
aliases:
  - body-alias
```

The router must ignore the body above when loading dispatch metadata.

---
> Source: [Q00/ouroboros](https://github.com/Q00/ouroboros) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-26 -->
