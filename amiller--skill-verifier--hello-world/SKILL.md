---
name: hello-world
description: Minimal example skill Use when this capability is needed.
metadata:
  author: amiller
---

# Hello World Skill

The simplest possible skill. Just prints a message and exits successfully.

## Usage

```bash
curl -X POST http://localhost:3000/verify \
  -H "Content-Type: application/json" \
  -d '{"skillPath": "./examples/hello-world"}'
```

Expected result: `passed: true`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amiller) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
