---
name: context-synthesis
description: MANDATORY synthesis of Brett's established context BEFORE answering technical questions. Uses recall for dynamic context — no hardcoded data. Triggers when Brett mentions homelab, networking, self-hosted software, cameras, automations, MCP servers, code projects, or uses phrases like "my setup", "my server". Use when this capability is needed.
metadata:
  author: scarabone
---

# Context Synthesis Skill

Before responding to technical questions, synthesize relevant context from recall and the current conversation. All infrastructure, project, and preference data lives in Reeve Memory — use `recall` to load it dynamically.

## Application Pattern

**Before responding, check:**
1. Does this touch their homelab? → `recall("homelab infrastructure Proxmox Docker setup", 5, "technical")`
2. Is this about automation? → `recall("Home Assistant automations integrations", 5, "technical")`
3. Involves code/development? → `recall("coding preferences development environment tools", 5, "preferences")`
4. References "the setup" or "my system"? → `recall("infrastructure network setup configuration", 5, "technical")`
5. Builds on previous work? → `recall` with the specific project name
6. Mentions self-hosted software? → `recall("self-hosted services Docker containers", 5, "technical")`

**When NOT to apply:**
- Theoretical questions with no implementation component
- Questions explicitly about different setups/environments
- Initial exploration of completely new topics

## Response Adjustments

**Skip redundant questions:**
- ✗ "Are you running Home Assistant in Docker or as an OS?"
- ✓ Use recall results — apply known setup without re-asking

**Suggest consistent approaches:**
- ✗ "You could try X, Y, or Z"
- ✓ "Following your backup-first approach, let's snapshot first"

**Build on previous work:**
- ✗ Provide generic solution
- ✓ "We can extend your existing MCP server to handle this"

**Respect constraints:**
- ✗ Ignore timing considerations
- ✓ "Since you're on the hospital schedule, this can run during your admin day"

## Context Loading Efficiency

Keep context application lightweight — reference only what's directly relevant. Don't recite the user's entire setup unless they're asking for an overview.

**Good:** "I'll add this to your Docker stack on Proxmox"
**Bad:** "Since you're running a Dell XPS with Proxmox hosting Docker containers managed by Portainer..."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/scarabone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
