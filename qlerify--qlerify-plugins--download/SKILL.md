---
name: download
description: > Use when this capability is needed.
metadata:
  author: qlerify
---

# Fast Download Qlerify Data

**CRITICAL:** When saving ANY Qlerify data to a file, use `curl + jq` instead of MCP tools. MCP responses pass through
AI context which takes minutes for large data. Shell pipes take seconds.

## Step 1: Find MCP credentials

```bash
cat ~/.claude.json 2>/dev/null | jq -r '.mcpServers.qlerify // empty'
```

Extract the `url` and `headers.x-api-key`.

## Step 2: Generic pattern for any MCP tool

```bash
curl -s "$MCP_URL" \
  -H "x-api-key: $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "tools/call",
    "params": {
      "name": "TOOL_NAME",
      "arguments": { ...ARGS... }
    }
  }' | jq -r '.result.content[0].text | fromjson' > output.json
```

## Common examples

### Full workflow → JSON file
```bash
curl -s "$MCP_URL" -H "x-api-key: $API_KEY" -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"get_workflow","arguments":{"workflowId":"...","projectId":"..."}}}' \
  | jq -r '.result.content[0].text | fromjson | .specification' > workflow.json
```

### OpenAPI spec → YAML file
```bash
curl -s "$MCP_URL" -H "x-api-key: $API_KEY" -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"generate_openapi_spec","arguments":{"workflowId":"...","projectId":"...","boundedContext":"..."}}}' \
  | jq -r '.result.content[0].text' > swagger.yaml
```

### Entities from workflow → JSON file
```bash
curl -s "$MCP_URL" -H "x-api-key: $API_KEY" -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"get_workflow","arguments":{"workflowId":"...","projectId":"..."}}}' \
  | jq -r '.result.content[0].text | fromjson | .specification.schemas.entities' > entities.json
```

### Domain events from workflow → JSON file
```bash
curl -s "$MCP_URL" -H "x-api-key: $API_KEY" -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"get_workflow","arguments":{"workflowId":"...","projectId":"..."}}}' \
  | jq -r '.result.content[0].text | fromjson | .specification.domainEvents' > events.json
```

## When to use what

| Data size          | Method    | Example                                 |
|--------------------|-----------|-----------------------------------------|
| Small (< 50 lines) | MCP tool  | `list_workflows`                        |
| Large (> 50 lines) | curl + jq | `get_workflow`, `generate_openapi_spec` |
| Any "save to file" | curl + jq | Always, regardless of size              |

## Finding IDs first

Use MCP tools for small lookups:
- `list_workflows` → get workflow ID and project ID

Then use curl for the actual data fetch.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qlerify) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
