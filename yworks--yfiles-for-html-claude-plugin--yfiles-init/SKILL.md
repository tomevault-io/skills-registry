---
name: yfiles-init
description: description: This skill should be used when the user asks to "initialize a yFiles application", "create a new yFiles demo", "scaffold a yFiles project", "set up GraphComponent", or mentions "create app", "new demo", "initialize yFiles", "setup yFiles", or "License.value". Use when this capability is needed.
metadata:
  author: yworks
---
---
name: yfiles-init
description: This skill should be used when the user asks to "initialize a yFiles application", "create a new yFiles demo", "scaffold a yFiles project", "set up GraphComponent", or mentions "create app", "new demo", "initialize yFiles", "setup yFiles", or "License.value".
---

# Initialize a yFiles Application

Create a complete yFiles application following package conventions.

## Quick Start

```
/yfiles-init [demo-name]
```

Creates in the current directory if no name is specified.

## Setup Checklist

```
- [ ] Verify GraphComponent API with yFiles MCP
- [ ] Create HTML entry point
- [ ] Create main TypeScript/JavaScript file
- [ ] Adjust import paths for license
- [ ] Configure ESLint with yFiles plugin (recommended)
- [ ] Test in browser
```

## Implementation

**Step 1: Verify APIs**

Use the yFiles MCP server to confirm the current API:

```
yfiles:yfiles_get_symbol_details(name="GraphComponent")
yfiles:yfiles_list_members(name="GraphComponent", includeInherited=true)
```

**Step 2: Create files**

Essential pattern:

```typescript
import { GraphComponent, GraphEditorInputMode, License } from '@yfiles/yfiles'
import licenseData from '../../lib/license.json'

License.value = licenseData  // MUST come first

const graphComponent = new GraphComponent('#graphComponent')
graphComponent.inputMode = new GraphEditorInputMode()
```

See `references/examples.md` for complete file templates.

**Step 3: Adjust import paths**

License path depends on demo location:
- `demos-ts/category/demo/` → `../../../lib/license.json`
- Adjust `../` depth to match directory nesting

**Step 4: Configure ESLint (Recommended)**

The official `@yfiles/eslint-plugin` helps catch common mistakes and ensures yFiles 3.0 best practices:

```bash
npm i -D eslint typescript typescript-eslint @yfiles/eslint-plugin
```

See [reference.md](reference.md) for complete ESLint configuration.

**Step 5: Test**

```bash
npm start
# Navigate to http://localhost:4242/demos-ts/your-demo/
```

## Key Rules

- **License first**: Call `License.value = licenseData` before any other yFiles API
- **CSS selectors**: Use `new GraphComponent('#id')` or pass a DOM element
- **Graph access**: `graphComponent.graph`
- **Set defaults before creating nodes**: Configure `graph.nodeDefaults.style` before `createNode()`

## Additional Resources

- **`references/examples.md`** — Complete HTML and TypeScript file templates
- **`references/reference.md`** — API patterns, GraphComponent options, input mode configuration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yworks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
