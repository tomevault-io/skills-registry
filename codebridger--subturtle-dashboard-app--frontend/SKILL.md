---
name: frontend-logic
description: Guidelines for Client-side logic, State Management (Pinia), API Integration, and App Navigation. Use when this capability is needed.
metadata:
  author: codebridger
---

# Frontend Logic Skill

## 1. Core Principles
- **State Management**: Use Pinia for global state.
- **API Integration**: Use `@modular-rest/client` to call server functions.
- **Testing**: Implement integration tests for complex component interactions.
- **Workflow**: Always reference the ClickUp task ID in commit messages (e.g., `feat: #taskid message`).

## 2. Documentation Reference
**You MUST read the following file for API Client details:**
- [Modular Rest Client Docs](./modular-rest_client.md)

## 3. Navigation & Auth Flow
The application uses hash-based routing (`/#/`).

### Common Routes
- Home: `/#/`
- Board: `/#/board`
- Settings: `/#/settings`

### Authentication
- Token is stored in `localStorage` key `token`.
- If redirected to login, verify token presence.

## 4. Key Patterns
- **Services**: Import `functionProvider` from `@modular-rest/client` to execute backend functions.
- **Composables**: Use Vue composables for reusable logic.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codebridger) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
