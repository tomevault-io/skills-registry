---
name: sparkgen-tool
description: Add, list, test, or remove MCP tools Use when this capability is needed.
metadata:
  author: praveengovianalytics
---

# SparkGen Tool

Manage MCP tools defined in `app/mcp_server.py` and `config/ai_workflow.yaml`.

## Dynamic Context

Before any action:
1. Read `app/mcp_server.py` — find `_define_tools()` method for current tool schemas
2. Read `config/ai_workflow.yaml` — parse `tools:` section
3. If server is running, fetch live tools: `curl -sf http://localhost:8000/v1/tools -H "X-API-Key: ${API_KEY:-dev-local-key}"`

## Actions

### List Tools (`/sparkgen-tool list`)
Parse `app/mcp_server.py` `_define_tools()` and display:
| Name | Description | Parameters | Target |
Also show which agents have each tool assigned (from workflow YAML `agents[].tools`).

### Add Tool (`/sparkgen-tool add <name>`)
Add a new MCP tool to the project. Modify these files:

1. **Tool schema** in `app/mcp_server.py` — add to `_define_tools()`:
   ```python
   Tool(
       name="<name>",
       description="<description>",
       inputSchema={
           "type": "object",
           "properties": {
               # define parameters
           },
           "required": [...]
       }
   )
   ```

2. **Handler method** in `app/mcp_server.py` — add `async def _handle_<name>(self, args)` method

3. **Call routing** in `app/mcp_server.py` — add case to `call_tool()` method:
   ```python
   elif name == "<name>":
       return await self._handle_<name>(arguments)
   ```

4. **Workflow entry** in `config/ai_workflow.yaml` — add to `tools.tools:`:
   ```yaml
   - name: <name>
     target: <target_type>
     resource: "<resource_id>"
   ```

5. **Agent assignment** — add tool name to relevant agent(s) `tools:` list in workflow YAML

6. **Test** — add test case in `tests/test_mcp_server.py`

7. **Validate**: Run `make validate` then `pytest tests/test_mcp_server.py -v`

### Test Tool (`/sparkgen-tool test <name>`)
If server is running:
```bash
curl -s -X POST http://localhost:8000/v1/tools/<name>/call \
  -H "Content-Type: application/json" \
  -H "X-API-Key: ${API_KEY:-dev-local-key}" \
  -d '{"arguments": {<test_args>}}'
```
Otherwise run the unit test: `pytest tests/test_mcp_server.py -v -k <name>`

### Remove Tool (`/sparkgen-tool remove <name>`)
1. Remove tool schema from `_define_tools()` in `app/mcp_server.py`
2. Remove handler method `_handle_<name>`
3. Remove case from `call_tool()`
4. Remove from `tools.tools:` in workflow YAML
5. Remove from all agent `tools:` lists in workflow YAML
6. Remove related tests
7. Run `make validate`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/praveengovianalytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
