---
name: playwright-browser-run-code
description: To run a custom Playwright script on the page, execute a code snippet for advanced interactions. Use when this capability is needed.
metadata:
  author: x-school-academy
---

## Usage
Use the MCP tool `dev-swarm.request` to send the payload as a JSON string:

```json
{"server_id":"playwright","tool_name":"browser_run_code","arguments":{}}
```

## Tool Description
Run Playwright code snippet

## Arguments Schema
The schema below describes the `arguments` object in the request payload.
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "code": {
      "type": "string",
      "description": "A JavaScript function containing Playwright code to execute. It will be invoked with a single argument, page, which you can use for any page interaction. For example: `async (page) => { await page.getByRole('button', { name: 'Submit' }).click(); return await page.title(); }`"
    }
  },
  "required": [
    "code"
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
