---
name: services-before-tokens
description: Routes tasks to external services (MCP, shell, n8n) before using AI tokens. Prevents wasteful AI processing for tool-solvable tasks. Use when this capability is needed.
metadata:
  author: bermingham85
---

# Services Before Tokens

## Trigger
Any task that could be handled by an external tool, API, MCP, or automation service.

## Action
**STOP** before generating AI output. Check if a service can do it instead:

### Routing Priority
1. File Operations → filesystem_mcp or shell commands
2. Database Queries → Direct PostgreSQL/SQL
3. Web Scraping → Playwright MCP
4. Calculations → Python/calculator
5. Git Operations → git CLI or GitHub MCP
6. Memory/Context → Memory MCP
7. n8n Workflows → Trigger webhook
8. Document Search → Notion MCP or grep
9. API Calls → Direct HTTP via curl/webhook

## Anti-Patterns (Token Waste)
- AI reads file and summarizes → Use read_files tool
- AI "searches" codebase → Use grep or codebase_semantic_search
- AI calculates → Python one-liner
- AI formats JSON → jq command

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bermingham85) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
