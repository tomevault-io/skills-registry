---
name: thor-maintenance
description: Maintain THOR installs using thor-util: update signatures, upgrade versions, download offline packs, generate reports, manage YARA-Forge. Use when the user asks about updating/upgrading/report generation. Use when this capability is needed.
metadata:
  author: nextronsystems
---
# THOR Maintenance Skill

Rules
- Be precise about thor-util verbs:
  - update = signatures
  - upgrade = program + signatures, keep config
  - download = full pack incl config (offline use case)
- Prefer stable signatures; mention sigdev only for urgent cases and explain tradeoffs.

Use these references when needed
- thor-util update vs upgrade vs download: reference/thor-util-update-upgrade.md
- YARA-Forge: reference/yara-forge.md
- Offline updates: reference/offline-updates.md

Output format
- Exact thor-util command(s)
- One-line “what it changes”
- One-line “what it does not change”

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nextronsystems) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
