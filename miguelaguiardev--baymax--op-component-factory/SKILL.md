---
name: op-component-factory
description: Creates new skills, agents, commands, and modes with consistent templates and validation flow.
metadata:
  author: miguelaguiardev
---

SKILL: COMPONENT FACTORY

Goal
Create new OpenCode components in a consistent and testable way.

Inputs
- component type: skill | agent | command | mode
- name
- description

Flow
1) Classify need with `workflow-classification`
2) Scaffold component:
- `./scripts/scaffold.sh <type> <name> "<description>"`
3) Fill content with operational details
4) Run checks:
- `./scripts/doctor.sh`
5) Sync:
- `./scripts/sync.sh push "<message>"`

Quality rules
- Keep front matter valid
- Keep names kebab-case
- Keep side effects gated by confirmation

Verification
- `opencode debug skill`
- `opencode agent list`
- `opencode debug config`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/miguelaguiardev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
