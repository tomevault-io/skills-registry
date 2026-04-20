---
name: documentation-assistant
description: Maintains and audits project documentation, including skill registries (AGENTS.md) and API references. Use when adding new skills, modifying backend routes, or ensuring documentation consistency. Use when this capability is needed.
metadata:
  author: sf-bcca
---

# Documentation Assistant

This skill automates the maintenance of ChoreWars project documentation.

## Capabilities

### 1. Update Agent Skills Registry
Synchronizes the `.gemini/skills/` directory with the `AGENTS.md` file to ensure the list of available skills is always up-to-date.

**Command:**
```bash
node .gemini/skills/documentation-assistant/scripts/update_agents.js
```

### 2. Generate API Reference
Scans the Fastify backend routes and generates a comprehensive `API_REFERENCE.md` file.

**Command:**
```bash
node .gemini/skills/documentation-assistant/scripts/generate_api_reference.js
```

## Workflows

### When adding a new Skill
1. Create the skill in `.gemini/skills/`.
2. Run the `update_agents.js` script to register it in `AGENTS.md`.

### When modifying API routes
1. After adding or changing routes in `server/src/routes/`, run the `generate_api_reference.js` script to refresh the documentation.

### Periodic Documentation Audit
1. Run both scripts to ensure the source of truth (code) matches the documentation.
2. Manually check `README.md` and `INSTALL.md` against current project state.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sf-bcca) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
