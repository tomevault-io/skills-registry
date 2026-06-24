---
name: nodered
description: Expert guidance for Node-RED flow-based programming. Use when working with Node-RED flows (JSON), creating custom nodes, configuring settings.js, debugging flows, or integrating with MQTT, HTTP, WebSocket, and Home Assistant. Triggers on tasks involving flow design, node development, automation workflows, and IoT integrations. Use when this capability is needed.
metadata:
  author: szkocot
---

# Node-RED

Node-RED is a flow-based programming tool for event-driven applications, enabling visual wiring of nodes to create automation workflows.

## Core Concepts

### Message Object

Messages flow between nodes via `msg` object:

```javascript
{
  payload: "data",      // Primary data
  topic: "category",    // Message type/category
  _msgid: "abc123"      // Auto-generated ID
}
```

### Node Types

- **Input**: Inject, HTTP In, MQTT In - initiate flows
- **Processing**: Function, Change, Switch - transform data
- **Output**: Debug, HTTP Response, MQTT Out - send results
- **Utility**: Delay, Trigger, Split/Join - control flow

## Flow JSON Structure

Flows stored in `flows.json`:

```json
[
  {
    "id": "node-uuid",
    "type": "inject",
    "name": "Timer",
    "repeat": "5",
    "payload": "hello",
    "payloadType": "str",
    "x": 100,
    "y": 100,
    "wires": [["next-node-id"]]
  },
  {
    "id": "next-node-id",
    "type": "debug",
    "name": "Output",
    "active": true,
    "complete": "payload",
    "x": 300,
    "y": 100,
    "wires": []
  }
]
```

**Key properties:**
- `id`: Unique UUID
- `type`: Node type name
- `x, y`: Editor position
- `wires`: Array of arrays mapping outputs to next node inputs

## Function Node

Execute JavaScript with full context access:

```javascript
// Access message
const data = msg.payload;

// Modify message
msg.payload = data.toUpperCase();
msg.timestamp = Date.now();

// Send to output
return msg;

// Multiple outputs
return [msg1, msg2];

// Send nothing
return null;
```

### Context Storage

```javascript
// Node context (this node only)
context.set("key", value);
const val = context.get("key");

// Flow context (all nodes in flow)
flow.set("counter", 0);
const count = flow.get("counter");

// Global context (all flows)
global.set("config", {});
const cfg = global.get("config");
```

### Async Operations

```javascript
// Using async/await
const result = await someAsyncFunction();
msg.payload = result;
return msg;

// Using node.send() for async
someAsyncFunction().then(result => {
  msg.payload = result;
  node.send(msg);
});
return null;  // Don't return msg here
```

## Common Nodes

### Change Node

Modify message properties without code:

- **Set**: `msg.payload` to value
- **Change**: Replace text in property
- **Move**: Rename property
- **Delete**: Remove property

### Switch Node

Route messages based on conditions:

- `==`, `!=`, `<`, `>`, `<=`, `>=`
- `contains`, `matches regex`
- `is null`, `is not null`
- `is of type` (string, number, etc.)

### Template Node

Mustache templating:

```
Hello {{payload.name}}!
Temperature: {{payload.temp}}°C
```

## Integrations

### MQTT

**Subscribe (MQTT In):**
```
Topic: home/sensors/#
QoS: 0/1/2
Output: parsed JSON or string
```

**Publish (MQTT Out):**
```
Topic: home/commands/light
Retain: true/false
QoS: 0/1/2
```

### HTTP

**Create endpoint (HTTP In → HTTP Response):**
```
Method: GET/POST/PUT/DELETE
URL: /api/data
```

**Make request (HTTP Request):**
```
Method: GET
URL: https://api.example.com/data
Return: parsed JSON
```

### WebSocket

Real-time bidirectional communication:
- WebSocket In: Listen for messages
- WebSocket Out: Send to clients

## Debugging

### Debug Node

- Output to sidebar panel
- Show complete msg or specific property
- Add to status bar

### Console Logging

```javascript
// In function node
node.warn("Warning message");   // Yellow, shows in debug
node.error("Error message");    // Red, triggers catch
console.log(msg);               // Terminal only
```

### Status Indicators

```javascript
node.status({
  fill: "green",    // green, yellow, red, grey
  shape: "dot",     // dot, ring
  text: "connected"
});
node.status({});    // Clear status
```

## Error Handling

### Catch Node

Catches errors from nodes in same flow:

```
[Any Node] → error → [Catch Node] → [Handle Error]
```

### Try/Catch in Function

```javascript
try {
  const result = JSON.parse(msg.payload);
  msg.payload = result;
  return msg;
} catch (e) {
  node.error("Parse failed: " + e.message, msg);
  return null;
}
```

## Configuration (settings.js)

Location: `~/.node-red/settings.js`

```javascript
module.exports = {
  // Server
  uiPort: 1880,
  httpAdminRoot: '/admin',
  httpNodeRoot: '/api',

  // Flows
  flowFile: 'flows.json',
  credentialSecret: "your-secret-key",

  // Security
  adminAuth: {
    type: "credentials",
    users: [{
      username: "admin",
      password: "$2b$08$hash...",  // bcrypt hash
      permissions: "*"
    }]
  },

  // Logging
  logging: {
    console: { level: "info" }
  },

  // Context storage
  contextStorage: {
    default: { module: "memory" },
    persistent: { module: "localfilesystem" }
  },

  // Function node globals
  functionGlobalContext: {
    moment: require('moment'),
    _: require('lodash')
  }
};
```

## Home Assistant Integration

Use `node-red-contrib-home-assistant-websocket`:

### Call Service Node

```
Domain: light
Service: turn_on
Entity: light.living_room
Data: {"brightness": 255}
```

### Entity Node

Monitor state changes:
```
Entity: sensor.temperature
Output on: state change
```

### Get Entities Node

Query current state:
```
Search: entity_id contains "light"
Output: array of entities
```

## Reference

- [Custom nodes](references/custom-nodes.md) - Full node development guide
- [Flow patterns](references/flow-patterns.md) - Common automation patterns
- [Home Assistant](references/home-assistant.md) - HA integration details

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/szkocot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
