---
name: node-example
description: Node.js skill with tests Use when this capability is needed.
metadata:
  author: amiller
---

# Node.js Example Skill

Demonstrates a Node.js skill with proper package.json and tests.

## Files

- `package.json` - Dependencies and test script
- `index.js` - Main skill code
- `test.js` - Simple test

## Testing Locally

```bash
npm install
npm test
```

## Verify

```bash
curl -X POST http://localhost:3000/verify \
  -H "Content-Type: application/json" \
  -d '{"skillPath": "./examples/node-app"}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amiller) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
