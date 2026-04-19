---
name: spreadjs-advanced-api
description: Access advanced SpreadJS functionality not covered by the standard API. Use when you need low-level spreadsheet operations like sparklines, slicers, cell buttons, advanced conditional formatting, outline groups, or any SpreadJS-specific features. Use when this capability is needed.
metadata:
  author: hewliyang
---

# SpreadJS Advanced API

## Raw Objects

Access the underlying SpreadJS objects:

```typescript
const rawWorkbook = workbook.originalWorkbook;  // GC.Spread.Sheets.Workbook
const rawSheet = workbook.getActiveSheet().originalSheet;  // GC.Spread.Sheets.Worksheet
// GC.Spread.Sheets.* namespace available for enums, constructors, etc.
```

## API Reference Structure

The type definitions in `api-reference.ts` are organized as nested modules under `GC`:

```
GC
├── Data              (line ~2)      - DataManager, Table, View, data binding
├── Pivot             (line ~3978)   - Pivot table types and enums
└── Spread
    ├── CalcEngine    (line ~4704)   - Formula engine, custom functions
    ├── Commands      (line ~5179)   - Command system, key bindings
    ├── Common        (line ~5424)   - Shared interfaces, culture info
    ├── Formatter     (line ~5949)   - Number/date formatting
    ├── Pivot         (line ~6225)   - Pivot table implementation
    ├── Report        (line ~11188)  - Report templates
    ├── Sheets        (line ~12641)  - Core spreadsheet (Workbook, Worksheet, Range, Style, etc.)
    └── Slicers       (line ~60687)  - Table/pivot slicers
```

## Searching the API

```bash
# Find a class and its methods
grep -n "export class Worksheet" api-reference.ts -A 200 | head -100

# Search for method names
grep -n "setSparkline\|getSparkline" api-reference.ts

# Find enums and their values
grep -n "export enum" api-reference.ts | head -30
grep -n "enum ChartType" api-reference.ts -A 50

# Search by keyword
grep -in "outline\|group" api-reference.ts | head -30

# Find interfaces for options/config objects
grep -n "export interface.*Options\|export type.*Options" api-reference.ts
```

## Creating Reusable Skills

When building a skill with helper functions, use the **loader pattern**:

### 1. Create `loader.js`

```javascript
// /files/skills/my-skill/loader.js
const _loadMySkill = new Function('GC', 'workbook', `
  function myHelper(sheet, arg1, arg2) {
    const rawSheet = sheet.originalSheet;
    // Use GC.Spread.Sheets.* APIs here
    return rawSheet.someMethod(arg1, arg2);
  }
  
  function anotherHelper(sheet, arg) {
    // ...
  }
  
  return { myHelper, anotherHelper };
`);
Object.assign(store, _loadMySkill(GC, workbook));
```

**Why `new Function()`?** Regular functions defined in `eval()` don't persist. The `Function` constructor creates functions that can be assigned to `store`.

**Note:** Escape backslashes in regex inside the template string: `\\d+` not `\d+`

### 2. Load and Use

```bash
# Step 1: Read loader into store (bash_command)
cat /files/skills/my-skill/loader.js
# Set to_store: "my_skill_loader"
```

```typescript
// Step 2: Initialize (execute_code) - only needed once per session
eval(store.my_skill_loader);

// Step 3: Use the helpers
store.myHelper(sheet, "arg1", "arg2");
```

### 3. Document in SKILL.md

```markdown
## Loading
1. bash: `cat /files/skills/my-skill/loader.js` → store
2. execute_code: `eval(store.my_skill_loader)`
3. Use: `store.myHelper(sheet, ...)`

## API
**myHelper(sheet, arg1, arg2)** - Description of what it does
```

## Cautions

1. Raw API changes may bypass dirty cell tracking (won't appear in review diff)
2. Prefer standard `workbook`/`sheet` API when available
3. Always search `api-reference.ts` for exact method signatures

## Reference

- Type definitions: `/skills/default/spreadjs-advanced-api/api-reference.ts`
- Online docs: https://developer.mescius.com/spreadjs/api/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hewliyang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
