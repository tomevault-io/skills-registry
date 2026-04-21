---
name: gitlab
description: Interact with GitLab using `glab` CLI. Use when this capability is needed.
metadata:
  author: vrslev
---

# GitLab Skill

Use the glab CLI to interact with GitLab.

```bash
# Get help
glab --help
glab <command> --help

# API access
glab api <endpoint> | jq .

# Common commands
glab mr list
glab issue list
glab repo clone <repo>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vrslev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
