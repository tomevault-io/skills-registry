---
name: prowler-mcp
description: > Use when this capability is needed.
metadata:
  author: prowler-cloud
---

## Overview

The Prowler MCP Server uses three sub-servers with prefixed namespacing:

| Sub-Server | Prefix | Auth | Purpose |
|------------|--------|------|---------|
| Prowler App | `prowler_app_*` | Required | Cloud management tools |
| Prowler Hub | `prowler_hub_*` | No | Security checks catalog |
| Prowler Docs | `prowler_docs_*` | No | Documentation search |

For complete architecture, patterns, and examples, see [docs/developer-guide/mcp-server.mdx](../../../docs/developer-guide/mcp-server.mdx).

---

## Critical Rules (Prowler App Only)

### Tool Implementation

- **ALWAYS**: Extend `BaseTool` (auto-registered via `tool_loader.py`, only public methods from the class are exposed as a tool)
- **NEVER**: Manually register BaseTool subclasses
- **NEVER**: Import tools directly in server.py

### Models

- **ALWAYS**: Use `MinimalSerializerMixin` for responses
- **ALWAYS**: Implement `from_api_response()` factory method
- **ALWAYS**: Use two-tier models (Simplified for lists, Detailed for single items)
- **NEVER**: Return raw API responses

### API Client

- **ALWAYS**: Use `self.api_client` singleton
- **ALWAYS**: Use `build_filter_params()` for query parameters
- **NEVER**: Create new httpx clients

---

## Hub/Docs Tools

Use `@mcp.tool()` decorator directly—no BaseTool or models required.

---

## Quick Reference: New Prowler App Tool

1. Create tool class in `prowler_app/tools/` extending `BaseTool`
2. Create models in `prowler_app/models/` using `MinimalSerializerMixin`
3. Tools auto-register via `tool_loader.py`

---

## QA Checklist (Prowler App)

- [ ] Tool docstrings describe LLM-relevant behavior
- [ ] Models use `MinimalSerializerMixin`
- [ ] API responses transformed to simplified models
- [ ] Error handling returns `{"error": str, "status": "failed"}`
- [ ] Parameters use `Field()` with descriptions
- [ ] No hardcoded secrets

---

## Resources

- **Full Guide**: [docs/developer-guide/mcp-server.mdx](../../../docs/developer-guide/mcp-server.mdx)
- **Templates**: See [assets/](assets/) for tool and model templates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/prowler-cloud) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
