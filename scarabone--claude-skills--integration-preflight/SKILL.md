---
name: integration-preflight
description: > Use when this capability is needed.
metadata:
  author: scarabone
---

# Integration Preflight Check

## Core Rule

**Verify requirements before implementation.**

If integration work takes >10 minutes, confirm the destination platform actually supports what you're building BEFORE starting.

## Triggers

Run this check when:
- Connecting to third-party APIs or services
- Setting up tunnels, proxies, or public endpoints
- Implementing authentication flows (OAuth, API keys, tokens)
- Deploying to cloud platforms
- Building for a specific client (web, mobile, desktop, CLI)
- Configuring webhooks or callbacks
- Any bridge between local code and external systems

## The Check (Do This First)

### 1. Identify the Target Platform
What specific platform/client will consume this integration?
- Claude.ai web ≠ Claude Desktop ≠ Claude Code ≠ Claude API
- GitHub.com ≠ GitHub Desktop ≠ GitHub CLI
- Be specific. "Claude" is not specific enough.

### 2. Query Official Docs via Context7

**Use Context7 to get authoritative answers.** Don't rely on training data.

```
mcp__context7__query-docs with:
- libraryId: [appropriate ID from table below]
- query: "[platform] [feature] authentication requirements"
```

#### Context7 Library IDs - Quick Reference

| Platform | Library ID | Snippets | Use For |
|----------|------------|----------|---------|
| **Claude Platform** | `/websites/platform_claude_en` | 32414 | API, MCP, integrations |
| **Claude Help** | `/websites/support_claude` | 1486 | Plans, features, limitations |
| **Anthropic Cookbook** | `/anthropics/anthropic-cookbook` | 1226 | Code examples |
| **Cloudflare Docs** | `/cloudflare/cloudflare-docs` | 20980 | Tunnels, Workers, Access |
| **Cloudflare Agents** | `/websites/developers_cloudflare_agents` | 1061 | AI agents, SDK |
| **Home Assistant** | `/home-assistant/home-assistant.io` | 7101 | User docs, integrations |
| **HA Developers** | `/home-assistant/developers.home-assistant` | 2045 | API, custom components |
| **MCP Spec** | `/modelcontextprotocol.io/llmstxt` | 1254 | Protocol, auth, transport |
| **Frigate** | `/blakeblackshear/frigate` | 1310 | NVR, object detection, config |
| **Frigate Docs** | `/websites/frigate_video` | 533 | Configuration, cameras |
| **Proxmox** | `/proxmox/pve-docs` | 1954 | API, VMs, containers |
| **Proxmox (alt)** | `/websites/pve_proxmox_pve-docs` | 1272 | Web UI, admin |
| **AdGuard Home** | `/adguardteam/adguardhome` | 278 | DNS, filtering, API |
| **Docker** | `/docker/docs` | 11763 | Containers, compose, API |
| **Docker (large)** | `/websites/docker` | 131291 | Comprehensive docs |

#### Example Queries

**Before building MCP integration for Claude.ai web:**
```
libraryId: /websites/platform_claude_en
query: "MCP server authentication requirements Claude web OAuth"
```

**Before setting up Cloudflare tunnel:**
```
libraryId: /cloudflare/cloudflare-docs
query: "Cloudflare tunnel authentication access control"
```

**Before building Home Assistant integration:**
```
libraryId: /home-assistant/developers.home-assistant
query: "Home Assistant API authentication long-lived access token"
```

### 3. Verify Feature Parity (Don't Assume)

| Assumption | Reality |
|------------|---------|
| "Works on desktop, will work on web" | Often false. Different auth, different capabilities. |
| "API supports it, web UI will too" | Web may have restrictions API doesn't. |
| "Mobile app same as desktop" | Feature sets often differ. |
| "Free tier has this" | May require paid plan. |

### 4. Confirm Before Proceeding

State findings to user:
- "Claude.ai web requires OAuth 2.1 for MCP servers. Authless only works with Claude Desktop."
- "This API requires Enterprise plan for webhook support."
- "Web interface doesn't support custom integrations, only the CLI does."

If requirements aren't met, STOP and discuss alternatives before building.

## Anti-Patterns (What Went Wrong)

### The Tunnel Disaster (2026-01-30)
**Task:** Expose MCP server to internet for Claude.ai web access
**Mistake:** Built entire Cloudflare tunnel infrastructure without checking Claude.ai web requirements
**Discovery:** Claude.ai web requires OAuth 2.1; authless servers only work with Claude Desktop
**Wasted:** ~2 hours of setup, configuration, testing, then rollback
**Fix:** 5-minute Context7 query would have revealed OAuth requirement before any implementation

### Pattern: Assumed Feature Parity
"Claude Desktop supports authless MCP, so Claude.ai web must too."

Wrong. Different clients have different security models.

## Decision Point

After the preflight check:

- **Requirements met?** → Proceed with implementation
- **Requirements unclear?** → Query Context7 again with more specific terms, or ask user
- **Requirements NOT met?** → STOP. Discuss alternatives. Don't build something that won't work.

## The 10-Minute Rule

If the integration will take more than 10 minutes to build:
1. Spend 5 minutes on preflight check (Context7 query)
2. Confirm requirements with user
3. Then build

Never invert this. Building first and checking later = wasted work.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/scarabone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
