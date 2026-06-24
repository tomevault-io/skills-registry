---
name: n8n-code-javascript
description: Write JavaScript code in n8n Code nodes. Use when writing custom JavaScript, accessing data in Code nodes, transforming data programmatically, handling multiple items, or debugging Code node errors. Use when this capability is needed.
metadata:
  author: fazer-ai
---

# n8n Code JavaScript

Expert guidance for writing JavaScript in n8n Code nodes.

> Based on [n8n-skills](https://github.com/czlonkowski/n8n-skills) by Romuald Członkowski

## Code Node Modes

### Run Once for All Items

Process all items together. **Most common mode**.

```javascript
// Access all input items
const items = $input.all();

// Process and return
return items.map(item => ({
  json: {
    ...item.json,
    processed: true
  }
}));
```

### Run Once for Each Item

Process each item separately.

```javascript
// Access current item
const data = $input.item.json;

// Return single item
return {
  json: {
    ...data,
    processed: true
  }
};
```

## Accessing Data

### Current Node Input

```javascript
// All items
const items = $input.all();

// First item
const first = $input.first();

// Current item (in "each item" mode)
const current = $input.item;

// JSON data
const data = $input.first().json;
```

### Previous Nodes

```javascript
// From specific node
const nodeData = $node["Node Name"].all();
const firstItem = $node["HTTP Request"].first().json;

// From input (same as previous node)
const inputItems = $input.all();
```

### Execution Context

```javascript
// Workflow info
const workflowId = $workflow.id;
const workflowName = $workflow.name;

// Execution info
const executionId = $execution.id;

// Environment variables
const apiKey = $env.API_KEY;

// n8n variables
const myVar = $vars.myVariable;
```

## Common Patterns

### Transform All Items

```javascript
const items = $input.all();

return items.map(item => ({
  json: {
    id: item.json.id,
    name: item.json.name.toUpperCase(),
    createdAt: new Date().toISOString()
  }
}));
```

### Filter Items

```javascript
const items = $input.all();

return items.filter(item => item.json.status === 'active');
```

### Aggregate Data

```javascript
const items = $input.all();

const total = items.reduce((sum, item) => sum + item.json.amount, 0);
const count = items.length;

return [{
  json: {
    total,
    count,
    average: total / count
  }
}];
```

### Group By

```javascript
const items = $input.all();

const grouped = {};
for (const item of items) {
  const key = item.json.category;
  if (!grouped[key]) grouped[key] = [];
  grouped[key].push(item.json);
}

return Object.entries(grouped).map(([category, items]) => ({
  json: { category, items, count: items.length }
}));
```

### Fetch External Data

```javascript
const response = await fetch('https://api.example.com/data', {
  headers: { 'Authorization': `Bearer ${$env.API_TOKEN}` }
});

const data = await response.json();

return [{ json: data }];
```

### Error Handling

```javascript
const items = $input.all();

return items.map(item => {
  try {
    const parsed = JSON.parse(item.json.rawData);
    return { json: { success: true, data: parsed } };
  } catch (error) {
    return { json: { success: false, error: error.message } };
  }
});
```

## Webhook Data Access

**CRITICAL**: In Code nodes, webhook data is under `.body`:

```javascript
// ❌ Wrong
const message = $input.first().json.message;

// ✅ Correct - webhook data is in body
const message = $input.first().json.body.message;
const headers = $input.first().json.headers;
const query = $input.first().json.query;
```

## Date/Time with Luxon

n8n includes [Luxon](https://moment.github.io/luxon/) for dates:

```javascript
const { DateTime } = require('luxon');

// Current time
const now = DateTime.now();

// Parse date
const date = DateTime.fromISO('2024-01-15');

// Format
const formatted = now.toFormat('yyyy-MM-dd HH:mm:ss');

// Add/subtract
const nextWeek = now.plus({ days: 7 });
const yesterday = now.minus({ days: 1 });

// Compare
const isAfter = now > date;
const diff = now.diff(date, 'days').days;
```

## Binary Data

### Access Binary

```javascript
const items = $input.all();

for (const item of items) {
  if (item.binary?.data) {
    const buffer = await item.binary.data.toBuffer();
    const base64 = buffer.toString('base64');
    // Process binary...
  }
}
```

### Create Binary

```javascript
const data = { key: 'value' };
const jsonString = JSON.stringify(data, null, 2);
const buffer = Buffer.from(jsonString);

return [{
  json: { fileName: 'data.json' },
  binary: {
    data: await this.helpers.prepareBinaryData(buffer, 'data.json', 'application/json')
  }
}];
```

## Common Mistakes

### 1. Wrong Return Format

```javascript
// ❌ Wrong - must return array of objects with json property
return { name: 'John' };

// ✅ Correct
return [{ json: { name: 'John' } }];
```

### 2. Not Returning All Items

```javascript
// ❌ Wrong - returns only first item
const item = $input.first();
return [{ json: item.json }];

// ✅ Correct - process all items
return $input.all().map(item => ({ json: item.json }));
```

### 3. Accessing Wrong Data Level

```javascript
// ❌ Wrong - items don't have .data
const data = items[0].data;

// ✅ Correct - use .json
const data = items[0].json;
```

### 4. Missing await for Async

```javascript
// ❌ Wrong - fetch is async
const response = fetch(url);

// ✅ Correct
const response = await fetch(url);
const data = await response.json();
```

## Available Libraries

Built-in libraries available in Code node:

- `luxon` - Date/time handling
- `lodash` - Utility functions
- `crypto` - Cryptographic functions

```javascript
const { DateTime } = require('luxon');
const _ = require('lodash');
const crypto = require('crypto');

// Use lodash
const grouped = _.groupBy(items, 'category');

// Use crypto
const hash = crypto.createHash('sha256').update(data).digest('hex');
```

## Best Practices

1. **Always return array** of `{ json: {...} }` objects
2. **Use `try/catch`** for error-prone operations
3. **Access webhook data** via `.body`
4. **Use Luxon** for dates, not raw Date()
5. **Validate input data** before processing
6. **Log sparingly** - console.log works but adds overhead

## Quick Reference

| Need | Code |
|------|------|
| All items | `$input.all()` |
| First item | `$input.first()` |
| Current item | `$input.item` (each mode) |
| Item JSON | `item.json` |
| Webhook body | `item.json.body` |
| Other node | `$node["Name"].first().json` |
| Env var | `$env.VAR_NAME` |
| Return item | `{ json: {...} }` |
| Return many | `[{ json: {...} }, ...]` |

---

<sub>Based on [n8n-skills](https://github.com/czlonkowski/n8n-skills) by Romuald Członkowski • Adapted for Moltbot by fazer.ai</sub>

---
> Source: [fazer-ai/moltbot-skill-n8n](https://github.com/fazer-ai/moltbot-skill-n8n) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-10 -->
