---
name: js-executor
description: Execute JavaScript code using Node.js vm Use when this capability is needed.
metadata:
  author: hermit403
---

This skill allows executing JavaScript code for code analysis and testing purposes.

## Capabilities

- Execute JavaScript code in a Node.js `vm` sandbox
- Pass custom context variables
- Retrieve execution results
- Support for modern ES6+ syntax via Node.js

## Usage

The skill accepts the following parameters:
- `code` (required): JavaScript code to execute
- `context` (optional): Dictionary of variables to initialize the execution context

## Example

```javascript
// Boolean logic
const a = true;
const b = false;
a && b; // return false
```

## Notes

- Timeout is set to 5 seconds

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hermit403) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
