---
name: playwright-browser-evaluate
description: To run JavaScript in the page context, evaluate an expression on the page or a specific element for inspection or manipulation. Use when this capability is needed.
metadata:
  author: x-school-academy
---

## Usage
Use the MCP tool `dev-swarm.request` to send the payload as a JSON string:

```json
{"server_id":"playwright","tool_name":"browser_evaluate","arguments":{}}
```

## Tool Description
Evaluate JavaScript expression on page or element

## Arguments Schema
The schema below describes the `arguments` object in the request payload.
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "function": {
      "type": "string",
      "description": "() => { /* code */ } or (element) => { /* code */ } when element is provided"
    },
    "element": {
      "description": "Human-readable element description used to obtain permission to interact with the element",
      "type": "string"
    },
    "ref": {
      "description": "Exact target element reference from the page snapshot",
      "type": "string"
    }
  },
  "required": [
    "function"
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
