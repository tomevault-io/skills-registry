---
name: mcp-server-design
description: Design and ship Model Context Protocol (MCP) servers that LLM clients (Claude Desktop, IDEs, agents) can use safely and efficiently. Use when exposing internal data, tools, or workflows to AI clients without writing N bespoke integrations. Use when this capability is needed.
metadata:
  author: SwapnilPopat
---

# MCP Server Design

Model Context Protocol (MCP) is the de facto standard for exposing tools, resources, and prompts to LLM clients. Done well, one MCP server replaces dozens of one-off integrations.

## When to Use

- You want Claude Desktop, Cursor, VS Code, Zed, or a custom agent to access an internal system.
- You're tempted to write the same "OpenAPI → tool wrapper" code for the third time.
- You have a domain (Jira, GitHub Enterprise, an internal data warehouse) multiple AI clients should access uniformly.
- You're building an agent and want its tool surface decoupled from the agent runtime.

## Concept Refresher

MCP defines three primitive kinds of capability a server exposes to a client:

| Primitive | Purpose | LLM-controlled? |
| --- | --- | --- |
| **Tool** | An action the model decides to call | Yes — model picks |
| **Resource** | Read-only context the host or user attaches | No — user/host attaches |
| **Prompt** | Reusable templated workflow the user invokes | No — user triggers |

Plus: roots (workspace scoping), sampling (server can ask the host LLM to complete), elicitation (server can ask the user a question), notifications (push updates).

## When To Use Which Primitive

- **Tool**: anything the agent should be able to invoke autonomously (search, lookup, write).
- **Resource**: a file, doc, table, or query result the user wants the model to "see" but not necessarily call. Use when the data is large, paginated, or selected interactively.
- **Prompt**: a multi-step recipe the user kicks off (e.g., "/code-review", "/triage-incident"). The server crafts the messages; the host runs the LLM.

If you're modeling a database, `query_table` is a tool; the table's schema is a resource; "explore_table" wizard is a prompt.

## Server Design Rules

1. **Stable, narrow surface.** Every primitive is a public API contract; rename = breaking change.
2. **Declarative manifests.** Tool/resource schemas are machine-readable; clients render UIs from them.
3. **Self-documenting.** Descriptions are read by the model — write them like API docs (see `tool-use-design`).
4. **Stateless by default.** Statefulness lives in your backend, not in the MCP process.
5. **Transport-agnostic logic.** Support stdio for local clients and HTTP+SSE/streamable-HTTP for remote.
6. **Auth at the edge.** Authenticate the *user*, not the model.
7. **Cheap to launch.** stdio servers should start in < 200 ms; clients spin them up per session.

## Transport

| Transport | Use when |
| --- | --- |
| **stdio** | Local desktop clients, per-user processes (Claude Desktop, IDEs) |
| **Streamable HTTP** (current spec) | Remote, multi-tenant servers; long-running connections; horizontal scale |
| **SSE** (legacy) | Existing integrations; new servers should prefer streamable HTTP |

Most internal servers benefit from supporting both: stdio for individual developers, HTTP for shared/remote deployments.

## Authentication and Authorization

- Use the MCP auth flow (OAuth 2.1 with dynamic client registration) for remote servers.
- Bind every request to a user identity; never trust headers alone.
- Authorize per-tool, per-resource, per-tenant; deny by default.
- Token-bind sessions so a stolen token can't be reused elsewhere.
- For stdio: the parent process owns trust; pass credentials via env, not args (process listings).

## Tool Design (in MCP context)

All `tool-use-design` rules apply, plus:

- Use the JSON Schema fields MCP supports (`title`, `description`, `enum`, `examples`).
- Return `isError: true` with a structured error payload that teaches the model.
- Distinguish `read_only` vs mutating tools via annotations the host can surface in UI.
- Bound results: paginate, summarize, return `next_cursor`.
- Avoid catch-all tools (`run_query(sql)`); prefer narrow, parameterized tools.

## Resources

- Use stable URIs (`internal://orders/2026-04`); clients link/cite by URI.
- Support `resources/list` with cursors and search hints.
- Send `resources/updated` notifications when content changes.
- Honor MIME types — clients render text, JSON, images, audio differently.
- For very large resources, expose a search/summarize tool rather than a single huge resource.

## Prompts

- Parameterize with named arguments; the client renders a form.
- Compose: a prompt may emit messages that include resources by URI and recommend tools.
- Treat prompts as versioned artifacts; bump versions on user-visible changes.

## Sampling and Elicitation (Server → Client/User)

- **Sampling**: server asks the host to run an LLM completion (uses the user's chosen model and budget). Useful for server-side reasoning without bundling your own model.
- **Elicitation**: server asks the user a structured question mid-flow. Use for confirmations, missing parameters, choice between options. Don't abuse — every elicitation interrupts the user.

## Streaming and Progress

- Long-running tools: emit progress notifications so the host can show a spinner / percentage.
- Streaming tool results: chunk JSON (newline-delimited) or use the streamable-HTTP framing.
- Cancellation: respect client cancellation; stop background work; release resources.

## Performance and Scaling

- Cold start matters for stdio (per-session). Lazy-load heavy deps; precompile schemas.
- Cache tool list / resource list responses; invalidate on backend change.
- Rate-limit per user and per tool.
- For HTTP: horizontal scale stateless workers; sticky sessions only if you keep transient state.
- Observe: tokens-not-applicable here, but log per-call latency, error rate, args size, response size.

## Security Hardening

- Validate every tool arg server-side (schema + business rules).
- Sanitize anything the tool returns that came from external sources (web pages, user uploads) — see `prompt-injection-defense`.
- Don't echo user-controlled content as if it were instructions.
- Sandbox any tool that executes code or shells.
- Audit log every call: user, tool, args (redacted), outcome.
- Patch dependencies fast; an MCP server is a privileged bridge.

## Versioning and Compatibility

- Version the server (`server.version` capability) and individual tools (`name@semver` or annotations).
- Maintain compatibility for at least one minor cycle when removing or renaming.
- Announce breaking changes in `server/info` on connect.
- Keep a changelog the user can read in the host UI.

## Testing

- Unit-test tool handlers directly; don't rely on going through MCP plumbing.
- Integration-test with the official `mcp` CLI / inspector.
- Eval set: realistic LLM prompts → expected tool selection + args (see `llm-eval-harness`).
- Smoke-test against the actual host clients you support; SDK behavior varies.

## Anti-Patterns

- Wrapping a giant REST API as one tool per endpoint — overwhelms model selection. Curate the 5-15 the agent actually needs.
- Returning raw HTML / 10k-line JSON — wastes context and invites injection.
- Using sampling/elicitation as load-bearing logic — clients may not support them, and they're slow.
- Auth handled in the client only — the server must enforce.
- Per-tool descriptions that say "see API docs" — the model can't.
- Ignoring streamable-HTTP: SSE-only servers are now legacy.
- Stateful servers that crash on restart, losing user context.

## Quick Wins Checklist

- [ ] Tool/resource/prompt distinction matches actual usage
- [ ] Tool count under ~20 per server; or two-stage discovery
- [ ] Schemas closed; descriptions teach when (not) to use
- [ ] OAuth 2.1 with dynamic client registration on remote servers
- [ ] Per-user, per-tool authorization enforced server-side
- [ ] Progress + cancellation supported on long ops
- [ ] Audit logging with PII redaction
- [ ] Integration tests via the MCP inspector
- [ ] Streamable HTTP supported (not SSE-only)
- [ ] Server cold start < 200 ms for stdio

## References

- Model Context Protocol specification (modelcontextprotocol.io)
- Reference SDKs: `@modelcontextprotocol/sdk` (TypeScript), `mcp` (Python)
- MCP Inspector tool
- Anthropic, Cursor, Zed, VS Code MCP integration docs
- Related skills: `tool-use-design`, `agent-architecture-patterns`, `prompt-injection-defense`, `api-security-review`, `api-versioning-strategy`, `llm-application-security`

---
> Source: [SwapnilPopat/ai-assistant-skills](https://github.com/SwapnilPopat/ai-assistant-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
