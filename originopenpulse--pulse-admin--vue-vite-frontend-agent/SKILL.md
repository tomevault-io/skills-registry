---
name: vue-vite-frontend-agent
description: Build and maintain the Vue + Vite frontend for a front-backend separated business system. Use when Codex needs to scaffold or modify Vue pages, router structure, state management, login flows, permission-aware menus or buttons, API request wrappers, or frontend integration with a Spring Boot backend. Use when this capability is needed.
metadata:
  author: originOpenPulse
---

# Vue Vite Frontend Agent

## Overview

Implement the frontend side of the current project with a bias toward maintainable structure and clean integration points. Keep the UI aligned with login, role, and permission workflows defined by the backend.

## Workflow

1. Inspect the existing frontend structure before changing anything.
2. Identify the requested slice: scaffold, page work, auth flow, permission control, or API integration.
3. Preserve existing conventions for routing, components, styling, and request utilities.
4. Prefer small, cohesive changes that keep views, composables, stores, and API modules separated.
5. Verify the changed flow locally when the project has runnable commands.

## Frontend Priorities

- Use `Vue + Vite` conventions first.
- Keep authentication state explicit and easy to trace.
- Centralize token handling, request interceptors, and error handling.
- Implement route guards for unauthenticated and unauthorized access.
- Support permission-aware rendering for routes, menus, and action buttons.
- Keep forms and tables easy to extend for user, role, and permission management.

## Recommended Feature Breakdown

- Project bootstrap:
  Create the Vite app, base layout, router, store, API layer, and environment config.
- Login and session:
  Build the login page, token persistence, logout, current-user bootstrap, and expired-session handling.
- Permission management UI:
  Build pages for users, roles, permissions, and role assignment.
- Backend integration:
  Map frontend DTOs carefully to backend responses and normalize error states in one place.

## Guardrails

- Do not invent frontend permission codes; reuse backend-provided identifiers.
- Do not scatter auth logic across unrelated components.
- Do not hide integration mismatches; surface missing fields and contract ambiguities clearly.
- When the project already uses a UI framework or design system, extend it instead of replacing it.

---
> Source: [originOpenPulse/pulse-admin](https://github.com/originOpenPulse/pulse-admin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
