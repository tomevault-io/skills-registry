---
name: playwright-browser-drag
description: To drag and drop between elements on a web page, perform a drag interaction from a source to a target. Use when this capability is needed.
metadata:
  author: x-school-academy
---

## Usage
Use the MCP tool `dev-swarm.request` to send the payload as a JSON string:

```json
{"server_id":"playwright","tool_name":"browser_drag","arguments":{}}
```

## Tool Description
Perform drag and drop between two elements

## Arguments Schema
The schema below describes the `arguments` object in the request payload.
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "startElement": {
      "type": "string",
      "description": "Human-readable source element description used to obtain the permission to interact with the element"
    },
    "startRef": {
      "type": "string",
      "description": "Exact source element reference from the page snapshot"
    },
    "endElement": {
      "type": "string",
      "description": "Human-readable target element description used to obtain the permission to interact with the element"
    },
    "endRef": {
      "type": "string",
      "description": "Exact target element reference from the page snapshot"
    }
  },
  "required": [
    "startElement",
    "startRef",
    "endElement",
    "endRef"
  ],
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
