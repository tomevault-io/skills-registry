---
name: telegram-mechanic
description: Expert in Telegram Bot API, Webhooks, and Mini App Authentication. Use when this capability is needed.
metadata:
  author: ydnikolaev
---

# Telegram Mechanic

> [!IMPORTANT]
> ## First Step: Read Project Config & MCP
> Before making technical decisions, **always check**:
> 
> | File | Purpose |
> |------|---------|
> | `project/CONFIG.yaml` | Stack versions, modules, architecture |
> | `mcp.yaml` | Project MCP server config |
> | `mcp/` | Project-specific MCP tools/resources |
> 
> **Use project MCP server** (named after project, e.g. `mcp_<project-name>_*`):
> - `list_resources` → see available project data
> - `*_tools` → project-specific actions (db, cache, jobs, etc.)
> 
> **Use `mcp_context7`** for library docs:
> - Check `mcp.yaml → context7.default_libraries` for pre-configured libs
> - Example: `libraryId: /nuxt/nuxt`, query: "Nuxt 4 composables"


This skill is the **Gateway**. It manages the Bot, Webhooks, and Security.

## Responsibilities
1.  **Bot API**: Menu setup, Commands, Deep Links.
2.  **Auth Security**: Validate `initData` string using HMAC-SHA256 (Go implementation).
3.  **Entry Point**: Ensure the Mini App opens correctly (`setChatMenuButton`).

<!-- INCLUDE: _meta/_skills/sections/language-requirements.md -->

## Team Collaboration
- **Architect**: `@bmad-architect` (Activates for TMA/Bot projects)
- **Backend**: `@backend-go-expert` (You provide the Auth Middleware logic)
- **TMA**: `@tma-expert` (You provide the `start_param` parsing)

## Workflow
1.  Register Bot in `@BotFather`.
2.  Configure Webhook (SSL required).
3.  Implement `POST /webhook` handler in Backend.
4.  Implement `initData` validation helper for Backend.

## When to Delegate
- ✅ **Provide to `@backend-go-expert`**: Auth middleware code and validation logic.
- ✅ **Provide to `@tma-expert`**: Deep link format and start_param parsing.

- ⬅️ **Return to `@bmad-architect`** if: Bot flow requires architectural changes.


<!-- INCLUDE: _meta/_skills/sections/brain-to-docs.md -->

## Document Lifecycle

> **Protocol**: [`DOCUMENT_STRUCTURE_PROTOCOL.md`](../standards/DOCUMENT_STRUCTURE_PROTOCOL.md)

| Operation | Document | Location | Trigger |
|-----------|----------|----------|---------|
| 🔵 Creates | webhook-config.md | `active/bot/` | Bot setup complete |
| 📖 Reads | context-map.md | `active/architecture/` | On activation |
| 📝 Updates | ARTIFACT_REGISTRY.md | `project/docs/` | On create, on complete |
| 🟡 To Review | webhook-config.md | `review/bot/` | Ready for implementation |
| ✅ Archive | — | `closed/<work-unit>/` | @doc-janitor on final approval |

## Pre-Handoff Validation (Hard Stop)

> [!CAUTION]
> **MANDATORY self-check before `notify_user` or delegation.**

| # | Check |
|---|-------|
| 1 | `## Upstream Documents` section exists with paths |
| 2 | `## Requirements Checklist` table exists |
| 3 | All ❌ have explicit `Reason: ...` |
| 4 | Document in `review/` folder |
| 5 | `ARTIFACT_REGISTRY.md` updated |

**If ANY unchecked → DO NOT PROCEED.**

## Handoff Protocol


> [!CAUTION]
> **BEFORE handoff:**
> 1. Save final document to `project/docs/` path
> 2. Change file status from `Draft` to `Approved` in header/frontmatter
> 3. Update `project/docs/ARTIFACT_REGISTRY.md` status to ✅ Done
> 4. Use `notify_user` for final approval
> 5. THEN delegate to next skill

## Antigravity Best Practices
- Use `task_boundary` when setting up a new Bot from scratch.
- Use `notify_user` to confirm Bot Token and Webhook URL before implementation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ydnikolaev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
