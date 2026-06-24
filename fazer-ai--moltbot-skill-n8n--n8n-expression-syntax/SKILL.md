---
name: n8n-expression-syntax
description: Write correct n8n expressions in workflow fields. Use when writing expressions, accessing data from previous nodes, using $json, $input, $node references, JavaScript in expressions, date/time formatting, or debugging expression errors. Use when this capability is needed.
metadata:
  author: fazer-ai
---

# n8n Expression Syntax

Expert guide for writing correct n8n expressions in workflows.

> Based on [n8n-skills](https://github.com/czlonkowski/n8n-skills) by Romuald Członkowski

## Expression Basics

n8n expressions use `{{ }}` syntax and are evaluated at runtime:

```javascript
// Access current item data
{{ $json.fieldName }}

// Access nested data
{{ $json.user.email }}

// Access array element
{{ $json.items[0].name }}
```

## Data References

### Current Node Data

```javascript
// Most common - current item's JSON data
{{ $json.propertyName }}

// Binary data reference
{{ $binary.data.fileName }}

// Current item index (in loops)
{{ $itemIndex }}
```

### Previous Node Data

```javascript
// From specific node by name
{{ $node["Node Name"].json.field }}

// From input (previous node)
{{ $input.item.json.field }}

// First item from a node
{{ $node["HTTP Request"].first().json.data }}

// All items from a node
{{ $node["Webhook"].all() }}
```

### Execution Context

```javascript
// Workflow info
{{ $workflow.id }}
{{ $workflow.name }}

// Execution info
{{ $execution.id }}
{{ $execution.resumeUrl }}

// Current date/time
{{ $now }}
{{ $today }}
```

## Common Patterns

### Webhook Data Access

**CRITICAL**: Webhook data is nested under `.body`:

```javascript
// ❌ Wrong - this won't work
{{ $json.message }}

// ✅ Correct - access webhook body
{{ $json.body.message }}

// Headers and query params
{{ $json.headers["content-type"] }}
{{ $json.query.paramName }}
```

### Conditional Expressions

```javascript
// Ternary operator
{{ $json.status === "active" ? "Yes" : "No" }}

// Nullish coalescing
{{ $json.name ?? "Unknown" }}

// Optional chaining
{{ $json.user?.profile?.avatar }}
```

### String Operations

```javascript
// Template literal
{{ `Hello ${$json.name}!` }}

// String methods
{{ $json.email.toLowerCase() }}
{{ $json.text.split(",")[0] }}
{{ $json.name.trim() }}
```

### Date/Time Formatting

```javascript
// Current date formatted
{{ $now.format("YYYY-MM-DD") }}

// Parse and format
{{ DateTime.fromISO($json.date).toFormat("dd/MM/yyyy") }}

// Add/subtract time
{{ $now.plus({ days: 7 }).toISO() }}
{{ $now.minus({ hours: 2 }).toFormat("HH:mm") }}
```

### Number Operations

```javascript
// Math operations
{{ $json.price * 1.1 }}
{{ Math.round($json.value * 100) / 100 }}

// Parse numbers
{{ parseInt($json.count) }}
{{ parseFloat($json.amount) }}
```

### Array Operations

```javascript
// Array length
{{ $json.items.length }}

// Map/filter
{{ $json.users.map(u => u.name).join(", ") }}
{{ $json.items.filter(i => i.active) }}

// Find
{{ $json.products.find(p => p.id === "abc") }}
```

## Common Mistakes

### 1. Missing `.body` for Webhooks

```javascript
// ❌ Wrong
{{ $json.data }}

// ✅ Correct (webhook puts data in body)
{{ $json.body.data }}
```

### 2. Wrong Node Reference Syntax

```javascript
// ❌ Wrong - missing quotes
{{ $node[HTTP Request].json.data }}

// ✅ Correct
{{ $node["HTTP Request"].json.data }}
```

### 3. Accessing Non-Existent Properties

```javascript
// ❌ Might error if user doesn't exist
{{ $json.user.name }}

// ✅ Safe with optional chaining
{{ $json.user?.name ?? "N/A" }}
```

### 4. Expression vs Code Node

```javascript
// Expression fields ({{ }}) - single expressions only
{{ $json.name }}

// Code node - full JavaScript
const items = $input.all();
return items.map(item => ({
  json: { name: item.json.name.toUpperCase() }
}));
```

## Special Variables

| Variable | Description |
|----------|-------------|
| `$json` | Current item's JSON data |
| `$binary` | Current item's binary data |
| `$input` | Input from previous node |
| `$node["Name"]` | Access specific node's output |
| `$workflow` | Workflow metadata |
| `$execution` | Execution metadata |
| `$now` | Current DateTime (Luxon) |
| `$today` | Today at midnight (Luxon) |
| `$itemIndex` | Current item's index |
| `$runIndex` | Current run index (loops) |
| `$env` | Environment variables |
| `$vars` | n8n variables |

## Luxon DateTime Reference

n8n uses [Luxon](https://moment.github.io/luxon/) for dates:

```javascript
// Formatting
{{ $now.toFormat("yyyy-MM-dd HH:mm:ss") }}
{{ $now.toISO() }}
{{ $now.toLocaleString() }}

// Parsing
{{ DateTime.fromISO("2024-01-15") }}
{{ DateTime.fromFormat("15/01/2024", "dd/MM/yyyy") }}

// Manipulation
{{ $now.plus({ days: 30 }) }}
{{ $now.startOf("month") }}
{{ $now.endOf("week") }}

// Comparison
{{ $now > DateTime.fromISO($json.dueDate) }}
{{ $now.diff(DateTime.fromISO($json.created), "days").days }}
```

## Expression Debugging

1. **Use Set node** to test expressions before complex nodes
2. **Check data structure** with `{{ JSON.stringify($json) }}`
3. **Use optional chaining** `?.` to prevent null errors
4. **Console isn't available** - use Set node to inspect values

---

<sub>Based on [n8n-skills](https://github.com/czlonkowski/n8n-skills) by Romuald Członkowski • Adapted for Moltbot by fazer.ai</sub>

---
> Source: [fazer-ai/moltbot-skill-n8n](https://github.com/fazer-ai/moltbot-skill-n8n) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-10 -->
