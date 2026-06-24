---
name: build-mcp-server
description: >- Use when this capability is needed.
metadata:
  author: m00kk
---

# Build MCP Server

## Choose stack

| Preference | SDK | Transport |
|------------|-----|-----------|
| Python / data APIs | `mcp` + FastMCP | stdio (local), streamable HTTP (remote) |
| Node / TS monorepo | `@modelcontextprotocol/sdk` | same |

Default: **Python FastMCP** unless the project is TypeScript-only.

## Workflow

```
Progress:
- [ ] Define tools (actions) vs resources (read-only context)
- [ ] Scaffold server with schema validation (Pydantic / Zod)
- [ ] Implement handlers with least-privilege defaults
- [ ] Test with MCP Inspector or client config
- [ ] Document install in README (no secrets)
```

### Step 1: Scaffold (Python)

```bash
mkdir mcp-my-service && cd mcp-my-service
python -m venv .venv && source .venv/bin/activate
pip install "mcp[cli]" pydantic
```

Minimal server pattern:

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("my-service")

@mcp.tool()
def search_items(query: str, limit: int = 10) -> str:
    """Search items by query. Returns JSON string."""
    # implement; return json.dumps(results)
    ...

if __name__ == "__main__":
    mcp.run()
```

### Step 2: Tool design rules

- One tool = one user-visible action; name verbs (`search_items`, not `helper`)
- Input schemas strict; reject unknown fields
- Return structured text (JSON string) for agent parsing
- Never return raw secrets or full env dumps

### Step 3: Client config (Cursor)

Add to MCP settings (project or user):

```json
{
  "mcpServers": {
    "my-service": {
      "command": "python",
      "args": ["-m", "mcp_my_service"],
      "cwd": "${workspaceFolder}"
    }
  }
}
```

### Step 4: Verify

1. Server starts without import errors
2. Each tool callable with valid + invalid inputs (invalid must fail clearly)
3. Remote deploy: OAuth, rate limits, health check, no secrets in tool responses

## Anti-patterns

- Dumping entire databases via one giant `query` tool
- Shell execution without argument allowlists
- Hardcoded API keys (use env vars documented in `.env.example` only)

---
> Source: [m00kk/agent-skills-playbook](https://github.com/m00kk/agent-skills-playbook) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
