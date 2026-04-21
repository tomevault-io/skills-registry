---
name: aims-command-center-ui
description: > Use when this capability is needed.
metadata:
  author: boomerang9
---

# A.I.M.S. Command Center UI Skill

This skill defines control panels for Boomer_Angs and OpenClaw: where the user monitors agents, workflows, and environments and triggers actions.

Use with `aims-global-ui`.

## When to Use

Activate when:

- Editing `app/agents/**`, `app/boomer-angs/**`, `app/openclaw/**`,
  or similar control-center routes.
- The user mentions: "command center", "agent control", "OpenClaw dashboard".

---

## Layout

- Left Sidebar:
  - Sections like: Agents, Workflows, Environments, Logs, Settings.
- Top Strip (main area):
  - Environment selector (Dev / Staging / Prod).
  - Global status (OK / Degraded / Issues).
- Main Panels:
  - Agent/Boomer_Ang cards with status and quick actions.
  - Workflow run summaries.
- Logs / Activity:
  - Table or stream view for recent events, filterable.

---

## Rules

- Emphasize safety:
  - Confirm destructive commands (stop, delete) with modals.
- Use badges and color for status (running, idle, failed, queued).
- Make it easy to see "what is happening now" at a glance.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/boomerang9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
