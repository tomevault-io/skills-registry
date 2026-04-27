---
name: dart-get-widget-tree
description: To inspect the Flutter widget tree of the running app, retrieve the widget hierarchy after connecting to the Dart Tooling Daemon. Use when this capability is needed.
metadata:
  author: x-school-academy
---

## Usage
Use the MCP tool `dev-swarm.request` to send the payload as a JSON string:

```json
{"server_id":"dart","tool_name":"get_widget_tree","arguments":{}}
```

## Tool Description
Retrieves the widget tree from the active Flutter application. Requires "connect_dart_tooling_daemon" to be successfully called first.

## Arguments Schema
The schema below describes the `arguments` object in the request payload.
```json
{
  "type": "object"
}
```

## Background Tasks
If the tool returns a task id, poll the task status via the MCP request tool:

```json
{"server_id":"dart","method":"tasks/status","params":{"task_id":"<task_id>"}}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/x-school-academy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
