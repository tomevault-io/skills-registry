---
name: forge-mcp-server
description: Designing an MCP server LLM agents actually use well. Transport choice (stdio vs HTTP streaming), capability scoping, structured error responses the model can recover from, tool count limits, telemetry, auth, versioning. Contains ready-to-paste stdio server skeleton + remote HTTP server pattern. Use when building an MCP server that exposes tools/resources/prompts to Claude, Cursor, Codex, or any MCP client. Use when this capability is needed.
metadata:
  author: f4rkh4d
---

# forge-mcp-server

You are designing a server that LLM agents will call. The protocol is MCP, the audience is non-human. Default agent-written MCP servers expose too much surface, return error strings the model cannot recover from, and bake assumptions about a single client. This skill exists to fix that.

The single most important thing to remember: **the LLM is your user**. It reads tool descriptions, makes decisions about which tool to call, fills arguments from the user's request, and recovers from your error responses. Every choice you make is felt by the model first.

## Quick reference (the things you must never ship)

1. A stdio MCP server that writes to `stdout` (corrupts the protocol).
2. A server advertising `resources` capability but not implementing `resources/list`.
3. More than 20 tools registered on one server.
4. Tools returning errors as plain strings without a code.
5. A tool with no `inputSchema`.
6. Pagination missing on any `list_*` tool that can return >100 items.
7. Long-running tool (>10s) returning the answer synchronously instead of a job ID.
8. Remote server without auth (even "internal network" is not auth).
9. PII or argument values logged raw (use hashes/redaction).
10. Tool schema versioned by parameter name instead of by tool name.

## Hard rules

### Transport

**1. Stdio for local-only, integrated tools. SSE / HTTP streaming for remote.** Stdio servers run as child processes of the client (Claude Desktop, Claude Code) and have zero network surface. Remote servers expose HTTP and need real auth, rate limits, observability.

**2. Pick one transport per server, do not multiplex.** If you need both, ship two binaries that share a core library.

**3. Stdio servers log to stderr ONLY, never stdout.** Stdout IS the protocol channel. A single stray `console.log` corrupts every subsequent message.

```ts
// stdio MCP server in TypeScript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

// CRITICAL: redirect console.log to stderr at process start.
console.log = console.error;

const server = new Server({ name: "my-server", version: "0.1.0" }, { capabilities: { tools: {} } });

// ... register tools ...

const transport = new StdioServerTransport();
await server.connect(transport);
```

### Capability scoping

**4. A server exposes one cohesive surface.** A "Postgres MCP" is fine. A "Postgres + S3 + Slack + Email MCP" is not. The LLM has limited working memory for tool selection.

**5. Declare exactly the capabilities you implement.** If you only expose tools, do not advertise `resources` or `prompts`. The client sends discovery against what you declare; lying invites confused error paths.

```ts
// GOOD - explicit about what's available
const server = new Server(
  { name: "billing", version: "0.1.0" },
  { capabilities: { tools: {} } },  // ONLY tools
);

// BAD - advertises resources but has no resources/list handler
const server = new Server(
  { name: "billing", version: "0.1.0" },
  { capabilities: { tools: {}, resources: {} } },
);
```

**6. Tool count: target under 15, hard cap 20.** Above 20 and tool selection accuracy drops measurably across all current frontier models. If you need more, split the server.

### Auth and scoping

**7. Remote servers require auth even in private deployments.** "Internal network" is not auth. OAuth 2.1 preferred, bearer tokens at minimum.

**8. Per-tenant scoping enforced server-side, not by the model.** Never trust the model to pass the right `tenant_id`. Bind tenant identity to the auth token and ignore any conflicting parameter from the model.

```ts
// BAD - trusts the model
tools.register("list_orders", { tenant_id, ... }, async ({ tenant_id }) => {
  return db.orders.find({ tenant_id });
});

// GOOD - tenant comes from auth context, not model
tools.register("list_orders", { ... }, async (_, ctx) => {
  return db.orders.find({ tenant_id: ctx.session.tenant_id });
});
```

**9. Read tools and write tools are separately permissioned.** A token that can `list_users` should not automatically be able to `delete_user`.

### Error responses

**10. Errors are structured and actionable.**

```ts
// BAD - the model cannot recover from this
throw new Error("Invalid request");

// GOOD - structured with a code + recovery hint
import { McpError, ErrorCode } from "@modelcontextprotocol/sdk/types.js";

throw new McpError(
  ErrorCode.InvalidParams,
  "Field 'createdAt' is not a valid filter. Valid fields: 'created_at', 'updated_at', 'status'.",
);

// Or, for tool-level errors (returned as content, NOT thrown):
return {
  content: [{ type: "text", text: JSON.stringify({
    error: {
      code: "invalid_filter_field",
      message: "Field 'createdAt' is not a valid filter.",
      valid_fields: ["created_at", "updated_at", "status"],
      retryable: false,
    },
  }) }],
  isError: true,
};
```

**11. Idempotent operations return existing state on retry, not 409.** The model often retries when uncertain.

```ts
// instead of throwing duplicate-resource, return what would be created
const existing = await db.customers.findByEmail(input.email);
if (existing) {
  return ok({ customer: existing, was_created: false });
}
const created = await db.customers.create(input);
return ok({ customer: created, was_created: true });
```

**12. Distinguish "user error" from "transient" from "fatal."** Error codes the model can route on:

| Code | Recovery strategy |
| --- | --- |
| `invalid_argument` | Fix the call and retry |
| `not_found` | Different lookup, or report to user |
| `permission_denied` | Stop trying, report to user |
| `rate_limited` | Wait then retry |
| `transient_error` | Retry with backoff |
| `internal_error` | Stop, surface to user |

### Telemetry

**13. Log every tool call.** Tool name, argument hash (not raw arguments), duration, outcome, client identity.

```ts
import { createHash } from "node:crypto";

server.setRequestHandler(CallToolRequestSchema, async (request) => {
  const start = performance.now();
  const argHash = createHash("sha256")
    .update(JSON.stringify(request.params.arguments))
    .digest("hex")
    .slice(0, 16);

  try {
    const result = await dispatch(request);
    logger.info({
      tool: request.params.name,
      arg_hash: argHash,
      duration_ms: Math.round(performance.now() - start),
      outcome: "success",
    }, "mcp.tool.call");
    return result;
  } catch (err) {
    logger.error({
      tool: request.params.name,
      arg_hash: argHash,
      duration_ms: Math.round(performance.now() - start),
      outcome: "error",
      err,
    }, "mcp.tool.call");
    throw err;
  }
});
```

**14. Track per-tool error rate.** A tool with >20% error rate is a design problem. Either the description is misleading the model, or the schema does not match real usage.

**15. No PII in logs.** Hash or redact. Especially user identifiers, emails, document contents. MCP servers often sit close to sensitive data.

### Versioning

**16. MCP protocol version is announced on connect; pin a minimum.** Servers that promise compatibility with everything end up working with nothing.

**17. Tool schemas are versioned by name, not by parameter.** When you need a breaking change to `search_users`, ship `search_users_v2` and deprecate the original over two releases.

### Performance

**18. Tool calls under 5 seconds.** Above 10s the client may time out. Long operations return a job ID and expose a `get_status` tool to poll.

```ts
// for long-running operations
tools.register("export_report", inputSchema, async (input) => {
  const jobId = await jobs.enqueue("export_report", input);
  return ok({ job_id: jobId, estimated_seconds: 60 });
});

tools.register("get_job_status", { job_id: z.string() }, async ({ job_id }) => {
  const job = await jobs.get(job_id);
  return ok({ status: job.status, result_url: job.result_url });
});
```

**19. Pagination is cursor-based. Same rule as REST APIs.** A model asked to "fetch all users" should never get back 50,000 entries.

**20. Resource reads can be ranged.** If you expose a 10MB document, the model often only needs the first 2KB. Support a `range` parameter.

## Common AI-output patterns to reject

| Pattern | Why wrong | Fix |
| --- | --- | --- |
| `console.log(...)` in a stdio server | Corrupts protocol | Redirect to stderr |
| Plain `throw new Error("...")` | Model cannot recover | `McpError(ErrorCode.X, "message")` |
| Tool with `inputSchema: {}` | Model invents args | Explicit zod/JSON schema with descriptions |
| 30 tools on one server | Selection accuracy collapses | Split into 2-3 servers |
| `tenant_id` accepted from tool arguments | Trusts the model | Read from `ctx.session` |
| Synchronous 30s export | Client times out | Job ID + status tool |
| Raw email in logs | PII leak | Hash, or redact at logger |
| `list_users` no pagination | Returns 50K rows | Cursor + limit |
| Same capability advertised + not implemented | Confused discovery | Match `capabilities` to handlers |
| `latest` model alias inside the server | Drift | Pin model version where used |

## Worked example: a minimal stdio MCP server

```ts
// src/server.ts
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { CallToolRequestSchema, ListToolsRequestSchema, McpError, ErrorCode }
  from "@modelcontextprotocol/sdk/types.js";
import { z } from "zod";
import { logger } from "./logger.js";

// CRITICAL: stdio MUST be stderr only.
console.log = console.error;
console.info = console.error;

const SearchUsersInput = z.object({
  query: z.string().min(1).max(200).describe("Full-text query against name and email"),
  limit: z.number().int().positive().max(100).default(20).describe("Max results to return"),
});

const server = new Server(
  { name: "orders", version: "0.1.0" },
  { capabilities: { tools: {} } },
);

server.setRequestHandler(ListToolsRequestSchema, async () => ({
  tools: [
    {
      name: "search_users",
      description:
        "Searches the user directory by name or email. " +
        "Use when the user asks to find a person by partial information. " +
        "Do not use for exact ID lookups; use get_user_by_id instead.",
      inputSchema: {
        type: "object",
        properties: {
          query: { type: "string", description: "Full-text query against name and email", minLength: 1, maxLength: 200 },
          limit: { type: "integer", description: "Max results", minimum: 1, maximum: 100, default: 20 },
        },
        required: ["query"],
        additionalProperties: false,
      },
    },
    {
      name: "get_user_by_id",
      description: "Retrieves a single user by their UUID. Use when you have the exact user_id.",
      inputSchema: {
        type: "object",
        properties: { user_id: { type: "string", format: "uuid" } },
        required: ["user_id"],
        additionalProperties: false,
      },
    },
  ],
}));

server.setRequestHandler(CallToolRequestSchema, async (request) => {
  const start = performance.now();
  const { name, arguments: args } = request.params;

  try {
    if (name === "search_users") {
      const input = SearchUsersInput.parse(args);
      const users = await db.users.search(input.query, input.limit);
      return { content: [{ type: "text", text: JSON.stringify({ users, count: users.length }) }] };
    }
    if (name === "get_user_by_id") {
      const input = z.object({ user_id: z.string().uuid() }).parse(args);
      const user = await db.users.findById(input.user_id);
      if (!user) {
        return {
          content: [{ type: "text", text: JSON.stringify({ error: { code: "not_found", message: `User ${input.user_id} not found.`, retryable: false } }) }],
          isError: true,
        };
      }
      return { content: [{ type: "text", text: JSON.stringify({ user }) }] };
    }
    throw new McpError(ErrorCode.MethodNotFound, `Unknown tool: ${name}`);
  } finally {
    logger.info(
      { tool: name, duration_ms: Math.round(performance.now() - start) },
      "mcp.tool.call",
    );
  }
});

const transport = new StdioServerTransport();
await server.connect(transport);
logger.info("mcp.server.started");  // writes to stderr, safe
```

What this demonstrates: console redirected to stderr (rule 3); narrow capability set (`tools` only, rule 5); two tools with clear descriptions (rules 4-6); structured error response with code + retryable flag (rule 10); schema validation via zod (rule 13); per-call logging without arg values (rule 13, 15).

## Workflow

When designing an MCP server:

1. **State the one-sentence purpose.** "Exposes our internal user CRM to the model." If you cannot, the server is too broad.
2. **List the tools.** 5-15. Name + one-line purpose.
3. **Pick transport.** Local-only stdio or remote streaming.
4. **Design the error vocabulary first.** Codes and recovery strategies. Reuse everywhere.
5. **Write the smallest tool end to end** including error responses. Validate against a real LLM client before adding the next tool.
6. **Test with at least two clients** (Claude Desktop + Claude Code, or Claude + Cursor) before declaring done. Behavior diverges in subtle ways.

## Verification

```bash
bash skills/mcp/forge-mcp-server/verify/check_mcp_server.sh path/to/server.ts
```

Flags: `console.log` in stdio servers, tools without `inputSchema`, bare-string errors thrown.

## When to skip this skill

- You are calling MCP servers, not building them.
- One-shot integration that does not need to be MCP. A direct API call is often simpler.

## Related skills

- [`forge-mcp-tool-design`](../forge-mcp-tool-design/SKILL.md) - designing individual tools for LLM consumption.
- [`forge-mcp-resource`](../forge-mcp-resource/SKILL.md) - exposing read-only content via MCP.
- [`forge-api-design`](../../backend/forge-api-design/SKILL.md) - error shape parallels.
- [`forge-logging`](../../backend/forge-logging/SKILL.md) - structured logs, redaction.

---
> Source: [f4rkh4d/forge-skill](https://github.com/f4rkh4d/forge-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
