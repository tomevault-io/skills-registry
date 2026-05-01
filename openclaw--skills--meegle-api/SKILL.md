---
name: meegle-api
description: Meegle Open API skills index. Read credentials first; missing credentials → see meegle-api-credentials and remind user where to get them. Use when this capability is needed.
metadata:
  author: openclaw
---

# Meegle API (index)

Read **meegle-api-credentials** first (domain, token, context, headers); then the skill that matches your task. Use read-file on **Path**; `{baseDir}` = skill pack root.

| Order | Path | When to read |
|-------|------|--------------|
| 1 | **{baseDir}/meegle-api-credentials/SKILL.md** | Domain, token, context, headers — before any Meegle API call |
| 2 | **{baseDir}/meegle-api-users/SKILL.md** | User APIs (groups, members) |
| 3 | **{baseDir}/meegle-api-space/SKILL.md** | Space (project) operations |
| 4 | **{baseDir}/meegle-api-work-items/SKILL.md** | Work items CRUD, list, search |
| 5 | **{baseDir}/meegle-api-setting/SKILL.md** | Settings, types, fields, process |
| 6 | **{baseDir}/meegle-api-comments/SKILL.md** | Comments on work items |
| 7 | **{baseDir}/meegle-api-views-measurement/SKILL.md** | Views, kanban, Gantt, charts |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
