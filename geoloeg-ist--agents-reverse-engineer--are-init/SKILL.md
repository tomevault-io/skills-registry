---
name: are-init
description: Initialize agents-reverse-engineer configuration Use when this capability is needed.
metadata:
  author: geoloeg-ist
---

Initialize agents-reverse-engineer configuration in this project.

<execution>
1. **Read version**: Read `.claude/ARE-VERSION` → store as `$VERSION`. Show the user: `agents-reverse-engineer v$VERSION`

2. Run the agents-reverse-engineer init command:

```bash
npx agents-reverse-engineer@$VERSION init
```

This creates `.agents-reverse-engineer/config.yaml` configuration file.
</execution>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/geoloeg-ist) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
