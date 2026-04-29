---
name: skills-hub
description: Use the ThinkFleet Skills Hub CLI to search, install, update, and publish agent skills. Use when you need to fetch new skills on the fly, sync installed skills to latest or a specific version, or publish new/updated skill folders. Use when this capability is needed.
metadata:
  author: thinkfleetai
---

# ThinkFleet Skills Hub

Search
```bash
thinkfleet skills search "postgres backups"
```

Install
```bash
thinkfleet skills install my-skill
```

Update
```bash
thinkfleet skills update my-skill
thinkfleet skills update --all
```

List
```bash
thinkfleet skills list
```

Notes
- Skills are installed to the agent workspace: `./skills/<skill>/SKILL.md`
- Use `thinkfleet skills check` to verify all skill dependencies are met

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
