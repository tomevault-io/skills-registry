---
name: aims-workflow-ui
description: > Use when this capability is needed.
metadata:
  author: boomerang9
---

# A.I.M.S. Workflow Builder UI Skill

This skill defines the UI pattern for building and managing workflows and automations (e.g., n8n-backed flows, Boomer_Ang routines).

Use with `aims-global-ui`.

## When to Use

Activate when:

- Editing `app/workflows/**`, `app/automation/**`, or similar builder UIs.
- The user says: "automation builder", "workflow editor", "n8n flows UI".

---

## Layout

- Left Sidebar:
  - List of workflows with status and quick actions.
- Main Builder:
  - Visual or structured list of steps in order.
  - Each step clickable to edit.
- Right Config Panel (desktop) / bottom sheet (mobile):
  - Inputs for the selected step's settings.

Runs / Logs:

- Panel or tab for recent runs, statuses, and errors.

---

## Rules

- Make "Add step" and "Reorder steps" obvious.
- Clearly distinguish between editing a workflow vs viewing execution logs.
- Surface validation errors in a clear, actionable way.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/boomerang9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
