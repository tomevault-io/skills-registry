---
name: tech-dev
description: 技术开发阶段，MCP层服务整合 Use when this capability is needed.
metadata:
  author: weykon
---

# Tech-Dev Skill (Development Stage)

This skill handles MCP integration and third-party service configuration during development.

## v1 edit scope (hard limit)

For first pass, **only modify**:
- Project directory files (if template was applied)
- `.env.local` / `.env.development` / `.env.production`
- Configuration files from MCP snippets
- `.create-together/state.json` - update development status
- `.create-together/context.json` - add dev_config

**Do not**:
- Modify any files in `templates/built-in/*`
- Change `package.json` dependencies (unless MCP requires)
- Modify routing or layout structure (unless MCP requires)
- Introduce features beyond selected MCPs

## Dev workflow (required)

- Before starting: Read `state.json` and `context.json` to understand project state
- Before starting: Ensure required MCP snippets are available
- Before starting (ShipAny + Supabase): Verify `DB_SCHEMA` is set and matches the target schema
- Before starting (ShipAny + Supabase): **Hard requirement** ensure ShipAny tables exist in `DB_SCHEMA` (use `src/config/db/schema.ts`)
- Before starting (ShipAny + Supabase): If user-only actions are needed, explicitly instruct the user (e.g., provide `DATABASE_URL`, register account for admin)
- After finishing:
  - Clear Next.js cache: `rm -rf .next`
  - Run `npm run build` to validate integration
  - Run MCP-specific validation commands (see checklist)
  - Update `state.json` to `phase: "iterate-check"`

## Execution order

1. Understand development needs: `references/00-dev-workflow.md`
2. Review MCP layer: `references/01-mcp-layer.md`
3. Select and configure MCPs:
   - Auth: `references/02-mcp/auth.md`
   - Stripe: `references/02-mcp/stripe.md`
   - Analytics: `references/02-mcp/analytics.md`
   - Supabase: `references/02-mcp/supabase.md`
   - Vercel: `references/02-mcp/vercel.md`
4. If using ShipAny template + Supabase Postgres (**MANDATORY**):
   - Follow `dev/10-supabase-shipany.md`
   - Ensure ShipAny tables exist in `DB_SCHEMA` (especially `config` and RBAC tables)
   - Prefer Supabase MCP to create schema/tables if migrations are not run
   - If Vercel MCP is configured, update env vars there as well
   - Remind user to register an admin account before role assignment
   - If legacy tables exist (e.g. `users/prompts/templates`), migrate or clean up to avoid confusion
5. Validation checklist: `references/03-checklist.md`

## Bundled script

- `dev/scripts/shipany_schema_guard.py` - ShipAny schema guard (SQL generation + optional apply)
- TODO: `scripts/setup_integrations.py` - MCP integration automation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/weykon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
