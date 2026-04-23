---
name: building-cloudflare-mcp
description: Create MCP servers on Cloudflare Workers using the MCP Connector pattern. Use when user mentions "cloudflare mcp", "worker mcp", "mcp connector", or wants to deploy MCP tools on Cloudflare. Use when this capability is needed.
metadata:
  author: websmartteam
---

# Cloudflare MCP Connector

Build and deploy MCP (Model Context Protocol) servers on Cloudflare Workers using the MCP Connector pattern.

## Why Cloudflare Workers for MCP?

- **Global edge deployment** - Low latency worldwide
- **MCP Connector compatible** - Works with Claude API MCP integration
- **No cold starts** - Always-on serverless
- **Free tier** - 100K requests/day free
- **Easy authentication** - Headers-based auth

## Official Documentation References

When you need deeper context, fetch these with WebFetch:

| Topic | URL |
|-------|-----|
| **Code Mode Pattern** | https://www.anthropic.com/engineering/code-execution-with-mcp |
| Remote MCP Servers | https://platform.claude.com/docs/en/agents-and-tools/remote-mcp-servers.md |
| MCP Connector API | https://platform.claude.com/docs/en/agents-and-tools/mcp-connector.md |
| Agent SDK MCP | https://platform.claude.com/docs/en/agent-sdk/mcp.md |
| Claude Code MCP | https://code.claude.com/docs/en/mcp.md |
| Cloudflare Code Mode | https://blog.cloudflare.com/code-mode/ |

### Code Mode (Advanced Pattern)

For high-scale deployments with many tools, consider the **Code Mode** pattern:
- Present MCP tools as code APIs (filesystem structure)
- Agent writes code to call tools instead of direct tool calls
- **98.7% token savings** for large tool sets
- Filter/transform data in execution environment before returning

**Why it works**: LLMs have seen millions of real TypeScript examples in training, but only contrived synthetic tool-call examples.

**Cloudflare Agents SDK** (built-in Code Mode):
```typescript
import { codemode } from "agents/codemode/ai";

const {system, tools} = codemode({
  system: "You are a helpful assistant",
  tools: { /* tool definitions */ },
});

const stream = streamText({
  model: openai("gpt-5"),
  system,
  tools,
  messages: [{ role: "user", content: "..." }]
});
```

Docs: https://github.com/cloudflare/agents/blob/main/docs/codemode.md

## MCP Server Structure

### Minimal Worker Template

```typescript
// src/index.ts
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    // CORS preflight
    if (request.method === 'OPTIONS') {
      return new Response(null, {
        headers: {
          'Access-Control-Allow-Origin': '*',
          'Access-Control-Allow-Methods': 'POST, OPTIONS',
          'Access-Control-Allow-Headers': 'Content-Type, Authorization',
        },
      });
    }

    // Auth check
    const authHeader = request.headers.get('Authorization');
    if (authHeader !== `Bearer ${env.MCP_API_KEY}`) {
      return Response.json({ error: 'Unauthorized' }, { status: 401 });
    }

    // Parse MCP request
    const body = await request.json() as MCPRequest;

    // Handle MCP methods
    switch (body.method) {
      case 'tools/list':
        return Response.json({
          jsonrpc: '2.0',
          id: body.id,
          result: { tools: getToolsList() }
        });

      case 'tools/call':
        const result = await handleToolCall(body.params, env);
        return Response.json({
          jsonrpc: '2.0',
          id: body.id,
          result
        });

      default:
        return Response.json({
          jsonrpc: '2.0',
          id: body.id,
          error: { code: -32601, message: 'Method not found' }
        });
    }
  },
};

interface MCPRequest {
  jsonrpc: '2.0';
  id: string | number;
  method: string;
  params?: Record<string, unknown>;
}

interface Env {
  MCP_API_KEY: string;
  // Add other bindings (KV, D1, R2, etc.)
}

function getToolsList() {
  return [
    {
      name: 'example_tool',
      description: 'Description of what this tool does',
      inputSchema: {
        type: 'object',
        properties: {
          param1: { type: 'string', description: 'First parameter' },
        },
        required: ['param1'],
      },
    },
  ];
}

async function handleToolCall(params: { name: string; arguments: Record<string, unknown> }, env: Env) {
  switch (params.name) {
    case 'example_tool':
      return { content: [{ type: 'text', text: `Result: ${params.arguments.param1}` }] };
    default:
      throw new Error(`Unknown tool: ${params.name}`);
  }
}
```

### wrangler.toml

```toml
name = "my-mcp-server"
main = "src/index.ts"
compatibility_date = "2024-01-01"

[vars]
# Non-secret config here

# Secrets added via: wrangler secret put MCP_API_KEY
```

## Claude Code Configuration

### Add Remote MCP Server

```bash
# Using CLI (user scope for global access)
claude mcp add --scope user my-mcp-server \
  --transport http \
  --url "https://my-mcp-server.username.workers.dev" \
  --header "Authorization: Bearer YOUR_API_KEY"
```

### Manual Configuration (~/.claude.json)

```json
{
  "mcpServers": {
    "my-mcp-server": {
      "type": "http",
      "url": "https://my-mcp-server.username.workers.dev",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

### Auto-Approve Tools (~/.claude/settings.json)

```json
{
  "autoApproveTools": [
    "mcp__my-mcp-server__*"
  ]
}
```

## Tool Naming Convention

Tools from MCP servers follow this pattern:
```
mcp__<server-name>__<tool-name>
```

Examples:
- `mcp__my-mcp-server__example_tool`
- `mcp__supabase__execute_sql`
- `mcp__context7__query-docs`

## Authentication Patterns

### Bearer Token (Recommended)
```typescript
const authHeader = request.headers.get('Authorization');
if (authHeader !== `Bearer ${env.MCP_API_KEY}`) {
  return Response.json({ error: 'Unauthorized' }, { status: 401 });
}
```

### API Key Header
```typescript
const apiKey = request.headers.get('X-API-Key');
if (apiKey !== env.API_KEY) {
  return Response.json({ error: 'Unauthorized' }, { status: 401 });
}
```

### IP Allowlist (Additional Layer)
```typescript
const clientIP = request.headers.get('CF-Connecting-IP');
const allowedIPs = env.ALLOWED_IPS?.split(',') || [];
if (allowedIPs.length && !allowedIPs.includes(clientIP)) {
  return Response.json({ error: 'Forbidden' }, { status: 403 });
}
```

## Deployment Workflow

```bash
# 1. Create project
npm create cloudflare@latest my-mcp-server -- --template worker-typescript

# 2. Install dependencies
cd my-mcp-server
npm install

# 3. Add secrets
wrangler secret put MCP_API_KEY
# Enter your secure API key

# 4. Deploy
wrangler deploy

# 5. Add to Claude Code
claude mcp add --scope user my-mcp-server \
  --transport http \
  --url "https://my-mcp-server.username.workers.dev" \
  --header "Authorization: Bearer YOUR_API_KEY"

# 6. Restart Claude Code
# Exit and run: claude --resume
```

## Advanced: Cloudflare Bindings

### KV Storage
```typescript
// wrangler.toml
[[kv_namespaces]]
binding = "MY_KV"
id = "abc123"

// Usage
const value = await env.MY_KV.get('key');
await env.MY_KV.put('key', 'value');
```

### D1 Database
```typescript
// wrangler.toml
[[d1_databases]]
binding = "DB"
database_name = "my-db"
database_id = "abc123"

// Usage
const result = await env.DB.prepare('SELECT * FROM users WHERE id = ?')
  .bind(userId)
  .first();
```

### R2 Storage
```typescript
// wrangler.toml
[[r2_buckets]]
binding = "BUCKET"
bucket_name = "my-bucket"

// Usage
const object = await env.BUCKET.get('file.txt');
await env.BUCKET.put('file.txt', content);
```

## Troubleshooting

### MCP Not Loading
1. Check worker is deployed: `curl https://your-worker.workers.dev`
2. Verify auth header matches secret
3. Restart Claude Code after config changes

### Tools Not Appearing
1. Check `tools/list` response format
2. Verify tool schema is valid JSON Schema
3. Check Claude Code logs: `/mcp` command

### Permission Errors
1. Add to autoApproveTools in settings.json
2. Use wildcard: `mcp__my-mcp-server__*`

## Security Checklist

- [ ] Use strong, unique API keys (32+ chars)
- [ ] Store secrets via `wrangler secret put`
- [ ] Never commit secrets to git
- [ ] Consider IP allowlisting for sensitive MCPs
- [ ] Use HTTPS only (Cloudflare provides this)
- [ ] Implement rate limiting if needed
- [ ] Log access attempts for auditing

---

## Advanced: OAuth-Protected MCPs (Microsoft, Google, etc.)

**When API keys aren't enough**: Services like Microsoft 365, Google Workspace, and others REQUIRE OAuth 2.0 with user consent. This is fundamentally different from API key authentication.

### API Key vs OAuth MCPs

| Aspect | API Key MCP | OAuth MCP |
|--------|-------------|-----------|
| **Auth Type** | Static secret | User-delegated tokens |
| **Token Lifetime** | Never expires | 1 hour (access), 90 days (refresh) |
| **User Consent** | Not required | Required |
| **Complexity** | Simple | Complex |
| **Use Cases** | Your own services | Third-party services (M365, Google) |
| **State Management** | Stateless | Stateful (Durable Objects) |

### Required Packages for OAuth MCPs

```json
{
  "dependencies": {
    "@cloudflare/workers-oauth-provider": "^0.0.4",
    "agents": "^0.3.6",
    "hono": "^4.0.0"
  }
}
```

**⚠️ CRITICAL**: Keep `agents` package updated! Version 0.0.67 has SSE bugs that cause `waitUntil() tasks did not complete` errors. Use 0.3.6+.

### wrangler.toml for OAuth MCPs

```toml
name = "my-oauth-mcp"
main = "src/index.ts"
compatibility_date = "2024-12-01"
compatibility_flags = ["nodejs_compat"]

# Durable Object for state persistence
[[durable_objects.bindings]]
name = "MCP_OBJECT"
class_name = "MyMCP"

[[migrations]]
tag = "v1"
new_sqlite_classes = ["MyMCP"]

# KV for OAuth state
[[kv_namespaces]]
binding = "OAUTH_KV"
id = "your-kv-id"
```

### OAuth MCP Architecture

```typescript
// src/index.ts
import OAuthProvider from '@cloudflare/workers-oauth-provider';
import { Hono } from 'hono';
import { MyMCP } from './mcp-server';

export interface Env {
  // OAuth provider credentials
  CLIENT_ID: string;
  CLIENT_SECRET: string;

  // Cloudflare bindings
  MCP_OBJECT: DurableObjectNamespace;
  OAUTH_KV: KVNamespace;
  COOKIE_ENCRYPTION_KEY: string;
}

// Re-export the MCP Durable Object
export { MyMCP };

// Create OAuth handler with Hono
function createOAuthHandler() {
  const app = new Hono<{ Bindings: Env & { OAUTH_PROVIDER: any } }>();

  app.get('/', (c) => c.json({ status: 'healthy' }));

  // OAuth metadata for Claude's discovery
  app.get('/.well-known/oauth-authorization-server', (c) => {
    const baseUrl = new URL(c.req.url).origin;
    return c.json({
      issuer: baseUrl,
      authorization_endpoint: `${baseUrl}/authorize`,
      token_endpoint: `${baseUrl}/token`,
      registration_endpoint: `${baseUrl}/register`,
    });
  });

  // Authorization redirect to provider (e.g., Microsoft)
  app.get('/authorize', async (c) => {
    const oauthReqInfo = await c.env.OAUTH_PROVIDER.parseAuthRequest(c.req.raw);

    // Store state for callback
    const state = crypto.randomUUID();
    await c.env.OAUTH_KV.put(`oauth_state:${state}`, JSON.stringify({
      clientId: oauthReqInfo.clientId,
      redirectUri: oauthReqInfo.redirectUri,
      scope: oauthReqInfo.scope,
      state: oauthReqInfo.state,
      codeChallenge: oauthReqInfo.codeChallenge,
      codeChallengeMethod: oauthReqInfo.codeChallengeMethod,
    }), { expirationTtl: 600 });

    // Redirect to provider's auth URL
    const authUrl = buildProviderAuthUrl(c.env, state);
    return c.redirect(authUrl);
  });

  // Callback from provider
  app.get('/callback', async (c) => {
    const code = c.req.query('code');
    const state = c.req.query('state');

    // Retrieve stored state
    const storedState = await c.env.OAUTH_KV.get(`oauth_state:${state}`);
    if (!storedState) return c.text('Invalid state', 400);

    // Exchange code for tokens
    const tokens = await exchangeCodeForTokens(code, c.env);

    // Complete OAuth flow
    const { redirectTo } = await c.env.OAUTH_PROVIDER.completeAuthorization({
      request: JSON.parse(storedState),
      userId: tokens.userId,
      props: {
        accessToken: tokens.accessToken,
        refreshToken: tokens.refreshToken,
        tokenExpiresAt: Date.now() + (tokens.expiresIn - 300) * 1000,
      },
    });

    return c.redirect(redirectTo);
  });

  return app;
}

// Main export with OAuth Provider
export default new OAuthProvider({
  apiHandlers: {
    '/sse': MyMCP.serveSSE('/sse') as any,
    '/mcp': MyMCP.serve('/mcp') as any,
  },
  authorizeEndpoint: '/authorize',
  tokenEndpoint: '/token',
  clientRegistrationEndpoint: '/register',
  defaultHandler: createOAuthHandler() as any,
});
```

### MCP Durable Object with Token Refresh

```typescript
// src/mcp-server.ts
import { McpAgent } from 'agents/mcp';
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';

interface AuthProps {
  accessToken: string;
  refreshToken: string;
  tokenExpiresAt: number;
  email: string;
}

export class MyMCP extends McpAgent<Env, {}, AuthProps> {
  server = new McpServer({ name: 'my-mcp', version: '1.0.0' });

  // Cache for token refresh
  private cachedAccessToken: string | null = null;
  private cachedTokenExpiresAt: number | null = null;

  async init() {
    // Register your tools here
    this.server.tool('my_tool', 'Description', { /* schema */ },
      async (args) => {
        const token = await this.getValidAccessToken();
        // Use token to call external API
        return { content: [{ type: 'text', text: 'Result' }] };
      }
    );
  }

  /**
   * CRITICAL: Token refresh with persistence
   *
   * Common pitfalls to avoid:
   * 1. DON'T call this method recursively (infinite loop!)
   * 2. DO persist refreshed tokens via updateProps()
   * 3. DO cache tokens in memory for performance
   */
  async getValidAccessToken(): Promise<string> {
    const now = Date.now();

    // Check memory cache first
    if (this.cachedTokenExpiresAt && now < this.cachedTokenExpiresAt) {
      return this.cachedAccessToken!;
    }

    // Check Durable Object storage
    // ⚠️ Return props.accessToken directly - DON'T call getValidAccessToken() here!
    if (this.props.tokenExpiresAt && now < this.props.tokenExpiresAt) {
      this.cachedAccessToken = this.props.accessToken;
      this.cachedTokenExpiresAt = this.props.tokenExpiresAt;
      return this.props.accessToken;  // ✅ Correct
      // return await this.getValidAccessToken();  // ❌ INFINITE RECURSION!
    }

    // Token expired - refresh it
    const tokens = await this.refreshAccessToken(this.props.refreshToken);
    const newExpiresAt = Date.now() + (tokens.expiresIn - 300) * 1000;

    // Update memory cache
    this.cachedAccessToken = tokens.accessToken;
    this.cachedTokenExpiresAt = newExpiresAt;

    // ⚠️ CRITICAL: Persist to Durable Object storage!
    const updatedProps = {
      ...this.props,
      accessToken: tokens.accessToken,
      refreshToken: tokens.refreshToken,
      tokenExpiresAt: newExpiresAt,
    };
    await this.updateProps(updatedProps);  // This persists to SQLite

    return tokens.accessToken;
  }

  async refreshAccessToken(refreshToken: string): Promise<{
    accessToken: string;
    refreshToken: string;
    expiresIn: number;
  }> {
    // Implement provider-specific refresh logic
    const response = await fetch('https://provider.com/oauth/token', {
      method: 'POST',
      headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
      body: new URLSearchParams({
        client_id: this.env.CLIENT_ID,
        client_secret: this.env.CLIENT_SECRET,
        refresh_token: refreshToken,
        grant_type: 'refresh_token',
      }),
    });

    const data = await response.json() as any;
    return {
      accessToken: data.access_token,
      refreshToken: data.refresh_token || refreshToken,
      expiresIn: data.expires_in,
    };
  }
}
```

### OAuth Prompt Strategies

When redirecting to the provider's auth URL, the `prompt` parameter matters:

| Prompt Value | Behaviour | Use Case |
|--------------|-----------|----------|
| `none` | Silent auth, fails if consent needed | Token refresh |
| `login` | Forces login, skips consent if granted | Reconnection |
| `consent` | Forces full consent flow | **Avoid** - causes re-auth loop |
| `select_account` | Account picker, then consent if new | First-time setup |

**Recommendation**: Use `prompt: 'login'` for reconnections. Avoid `consent` as it forces re-authentication every time.

### Common OAuth MCP Pitfalls

1. **Infinite recursion in token refresh**
   - ❌ `return await this.getValidAccessToken()` inside itself
   - ✅ `return this.props.accessToken` directly

2. **Not persisting refreshed tokens**
   - ❌ Only updating memory cache
   - ✅ Call `await this.updateProps(updatedProps)` to persist to Durable Object

3. **Outdated `agents` package**
   - ❌ Version 0.0.67 has SSE bugs
   - ✅ Use version 0.3.6+ for stable SSE connections

4. **Using `prompt: 'consent'`**
   - ❌ Forces full re-authentication every connection
   - ✅ Use `prompt: 'login'` to skip consent if already granted

5. **Token refresh timing**
   - ❌ Refresh only when token is expired
   - ✅ Refresh 5 minutes before expiry (`expiresIn - 300`)

### Debugging OAuth MCPs

```bash
# Watch worker logs in real-time
wrangler tail

# Test health endpoint
curl https://your-worker.workers.dev

# Check OAuth metadata
curl https://your-worker.workers.dev/.well-known/oauth-authorization-server
```

### OAuth Token Lifetimes by Provider

| Provider | Access Token | Refresh Token | Notes |
|----------|--------------|---------------|-------|
| Microsoft 365 | 1 hour | 90 days | Refresh tokens rotate |
| Google | 1 hour | 6 months | Revoked if unused |
| GitHub | 8 hours | 6 months | Scoped to permissions |
| Slack | 12 hours | No expiry | Until revoked |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/websmartteam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
