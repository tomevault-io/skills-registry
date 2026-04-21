---
name: aims-crm-ui
description: > Use when this capability is needed.
metadata:
  author: boomerang9
---

# A.I.M.S. CRM UI Skill

This skill defines the CRM / client & project management pattern for A.I.M.S. Used when building Plugs that manage contacts, projects, or pipelines.

Use with `aims-global-ui`.

## When to Use

Activate when:

- Editing routes like `app/crm/**`, `app/clients/**`, `app/projects/**`.
- The user says "CRM", "client manager", "project board", or "pipeline".

---

## Layout

- Left Sidebar:
  - Sections like: Overview, Leads, Clients, Projects, Pipelines.
- Top Bar (main area):
  - Search field.
  - Filter controls (status, owner, priority).
  - "Add [Lead/Client/Project]" primary button.
- Main Content:
  - Either:
    - Table view with sortable columns and status chips, or
    - Kanban-style columns per stage.

Detail view:

- Desktop: slide-over panel from right.
- Mobile: full-screen modal.

---

## Rules

- Always show current status and next steps clearly.
- For long lists, support pagination or lazy load.
- Keep navigation, filters, and buttons consistent across CRM sections.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/boomerang9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
