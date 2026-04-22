---
name: mcp-builder
description: Guide for creating high-quality MCP (Model Context Protocol) servers that enable LLMs to interact with external services through well-designed tools. Use when building MCP servers to integrate external APIs or services, whether in Python (FastMCP) or Node/TypeScript (MCP SDK). Use when this capability is needed.
metadata:
  author: koenrohrer
---

# MCP Server Development Guide

Build MCP servers that enable LLMs to interact with external services through well-designed tools.

## Process

### Phase 1: Deep Research and Planning

1. **Research the API** you are integrating -- read docs, understand endpoints, auth, rate limits
2. **Plan tool coverage** -- balance comprehensive API endpoint coverage with specialized workflow tools
3. **Design tool naming** -- use consistent prefixes (e.g., `github_create_issue`, `github_list_repos`) and action-oriented naming
4. **Choose technology** -- TypeScript (MCP SDK) for most cases, Python (FastMCP) when the ecosystem favors it
5. **Choose transport** -- Streamable HTTP for remote/stateless, stdio for local servers

### Phase 2: Implementation

Build these core components:
- API client with authentication
- Error handling helpers with agent-friendly messages
- Response formatting (JSON for structured data, Markdown for display)
- Pagination support for list endpoints

**Input/Output schemas**: Use Zod (TypeScript) or Pydantic (Python). Include constraints, clear descriptions, and examples.

**Tool annotations**: Include `readOnlyHint`, `destructiveHint`, `idempotentHint`, and `openWorldHint`.

### Phase 3: Review and Test

- No duplicated code (DRY)
- Consistent error handling across all tools
- Full type coverage
- Clear, descriptive tool descriptions
- Test with MCP Inspector: `npx @modelcontextprotocol/inspector`

### Phase 4: Create Evaluations

Write evaluation questions that are:
- Independent (no shared state between questions)
- Read-only (don't modify external resources)
- Complex (require multiple tool calls)
- Realistic (mirror actual user tasks)
- Verifiable (have clear correct answers)
- Stable (answers don't change over time)

## Reference

- MCP specification: https://modelcontextprotocol.io/
- TypeScript SDK: https://github.com/modelcontextprotocol/typescript-sdk
- Python SDK: https://github.com/modelcontextprotocol/python-sdk

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/koenrohrer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
