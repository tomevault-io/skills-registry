---
name: playwright-browser-snapshot
description: To capture an accessibility snapshot of the current page, take a structured snapshot for UI inspection. Use when this capability is needed.
metadata:
  author: x-school-academy
---

## Usage
Use the MCP tool `dev-swarm.request` to send the payload as a JSON string:

```json
{"server_id":"playwright","tool_name":"browser_snapshot","arguments":{}}
```

## Tool Description
Capture accessibility snapshot of the current page, this is better than screenshot

## Arguments Schema
The schema below describes the `arguments` object in the request payload.
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "filename": {
      "description": "Save snapshot to markdown file instead of returning it in the response.",
      "type": "string"
    }
  },
  "additionalProperties": false
}
```

## Background Tasks
If the tool returns a task id, poll the task status via the MCP request tool:

```json
{"server_id":"playwright","method":"tasks/status","params":{"task_id":"<task_id>"}}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/x-school-academy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
