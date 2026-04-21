---
name: mcp-tool-creator
description: >- Use when this capability is needed.
metadata:
  author: nicolas-codemate
---

# MCP Tool Creator

Create a project-scoped MCP server that wraps `docker compose exec` commands into named tools.
The generated server lives in the project and is registered in `.mcp.json`.

## Workflow

Five interactive steps. Ask for user input at each step before proceeding.

### Step 1: Discover

<instructions>
1. Read `docker-compose.yml` (or `docker-compose.yaml`, `compose.yml`, `compose.yaml`) from project root
2. Parse the `services:` section to extract service names and images
3. For each service, detect the technology from the image name:
   - `php`, `php-fpm`, `symfony` -> PHP/Symfony
   - `node`, `bun`, `deno` -> Node.js
   - `postgres`, `pgsql` -> PostgreSQL
   - `mysql`, `mariadb` -> MySQL
   - `redis` -> Redis
   - `python`, `django`, `flask` -> Python
   - `elasticsearch`, `elastic`, `opensearch` -> Elasticsearch
   - Check `build:` context and Dockerfile for additional hints if image is custom
4. Present detected services with their technologies in a table
5. Ask the user:
   - Which services to expose as MCP tools (default: all detected)
   - Runtime preference: Python FastMCP (recommended) or Node.js SDK
</instructions>

<output>
Display a table:
| Service | Image | Detected Tech | Include? |
Then ask for confirmation and runtime choice.
</output>

### Step 2: Design

<instructions>
1. Load `references/docker-tool-patterns.md`
2. For each selected service, match patterns from the reference based on detected technology
3. Propose a list of tools with: name, command, parameters, description
4. Include relevant "General Docker" tools (logs, ps, restart)
5. Ask the user to:
   - Confirm, remove, or add tools
   - Customize tool names if desired
   - Add custom tools not in the patterns list
6. For database services, ask for default credentials (user, database name) from the compose env vars
</instructions>

<output>
Numbered list of proposed tools:
1. `php_lint` - Check PHP syntax (service: php)
2. `run_tests` - Run PHPUnit (service: php)
...
Ask: "Confirm this list? Add/remove tools? (numbers to remove, or describe custom tools)"
</output>

### Step 3: Generate

<instructions>
1. Read the appropriate template from `assets/`:
   - Python: `assets/fastmcp-server-template.py`
   - Node.js: `assets/nodejs-server-template.js`
2. Copy the template to `mcp-tools/server.py` (or `server.js`) at the project root
3. Replace `{{SERVER_NAME}}` with a descriptive name based on the project (e.g., `myapp-tools`)
4. Generate tool functions for each validated tool from Step 2

**Python tool generation pattern:**
```python
@mcp.tool()
async def tool_name(param: str = "default") -> str:
    """Tool description.

    Args:
        param: Parameter description.
    """
    return await run_docker_command("service", ["command", param])
```

**Node.js tool generation pattern:**
Add to TOOLS array:
```javascript
{
  name: "tool_name",
  description: "Tool description.",
  inputSchema: {
    type: "object",
    properties: {
      param: { type: "string", description: "Parameter description" },
    },
    required: [],
  },
},
```
Add to HANDLERS map:
```javascript
tool_name: async (args) => {
  return await runDockerCommand("service", ["command", args.param || "default"]);
},
```

5. Replace the `{{TOOLS_MARKER}}` / `{{HANDLERS_MARKER}}` comments with generated code
6. For database tools with credentials, read defaults from docker-compose.yml env vars
7. Show the generated file to the user for review
</instructions>

<constraints>
- Keep `run_docker_command` / `runDockerCommand` helper unchanged from template
- One tool = one function (Python) or one TOOLS entry + one HANDLERS entry (Node.js)
- Always use `-T` flag for docker compose exec (non-interactive)
- Tool parameters must have sensible defaults where possible
- Never hardcode passwords in tool code; read from environment variables
</constraints>

### Step 4: Configure

<instructions>
1. Check if `.mcp.json` exists at the project root
2. If it exists, read it and merge the new server entry
3. If it does not exist, create it
4. Add the server configuration:

**Python server (system python3):**
```json
{
  "mcpServers": {
    "{{SERVER_NAME}}": {
      "command": "python3",
      "args": ["mcp-tools/server.py"]
    }
  }
}
```

**Python server (venv, when system Python is locked):**
```json
{
  "mcpServers": {
    "{{SERVER_NAME}}": {
      "command": "mcp-tools/.venv/bin/python",
      "args": ["mcp-tools/server.py"]
    }
  }
}
```

**Node.js server:**
```json
{
  "mcpServers": {
    "{{SERVER_NAME}}": {
      "command": "node",
      "args": ["mcp-tools/server.js"]
    }
  }
}
```

5. Show the resulting `.mcp.json` to the user
</instructions>

<constraints>
- Never overwrite existing MCP server entries in `.mcp.json`
- Use relative paths for both `command` and `args` — do NOT set `cwd` (Claude Code resolves paths relative to `.mcp.json` location)
- Preserve existing JSON formatting and entries
</constraints>

### Step 5: Validate

<instructions>
1. **Syntax check:**
   - Python: run `python3 -c "import ast; ast.parse(open('mcp-tools/server.py').read())"`
   - Node.js: run `node --check mcp-tools/server.js`
2. **Dependencies check:**
   - Python: verify `mcp` package is available (`python3 -c "from mcp.server.fastmcp import FastMCP"`)
   - If import fails (PEP 668 / system Python locked), create a venv:
     `python3 -m venv mcp-tools/.venv && mcp-tools/.venv/bin/pip install mcp`
   - Then use `mcp-tools/.venv/bin/python` as the command in `.mcp.json`
   - Node.js: verify `@modelcontextprotocol/sdk` is in package.json or node_modules
3. If dependencies are missing, provide install command:
   - Python: `pip install mcp`, `uv pip install mcp`, or venv approach above
   - Node.js: `npm install @modelcontextprotocol/sdk`
4. **Startup test:** attempt to import/parse the server to catch runtime errors
5. Report results and next steps:
   - Remind user to restart Claude Code to pick up the new MCP server
   - List the tools that will be available
   - Suggest adding `mcp-tools/` to `.gitignore` if not tracked
</instructions>

<output>
Summary:
- Server: mcp-tools/server.py (or .js)
- Tools: N tools for M services
- Config: .mcp.json updated
- Status: Ready / Issues found

Next steps:
1. Restart Claude Code (or reload MCP servers)
2. The following tools are now available: [list]
</output>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nicolas-codemate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
