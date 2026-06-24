---
name: convert-miniapp
description: Converts a standalone browser-based JavaScript app into an OpenYoghurt mini-app with manifest, tool handlers, and Claude agent integration. Use when the user asks to convert, adapt, or integrate a JS app with the OpenYoghurt platform.
metadata:
  author: sinishadjukic
---

# Convert JavaScript App to OpenYoghurt Mini-App

Read the full conversion guide first: `docs/miniapp-conversion-guide.md`

## Steps

### 1. Analyze the Target App

Read the app's `index.html` and all JavaScript files to understand:

- **Data loading**: How is data loaded? (file upload, fetch, generated)
- **Global state**: Where is parsed data stored? (e.g., global arrays, objects)
- **Query/filter functions**: What existing query or aggregation APIs are there?
- **Script load order**: What scripts must load before the bridge?

### 2. Create `manifest.json`

```json
{
  "name": "App Name",
  "version": "1.0.0",
  "description": "What this app does",
  "dataInput": {
    "type": "file",
    "accepts": [".xlsx"],
    "description": "Human-readable description of accepted file types"
  },
  "mcpTools": []
}
```

Validation rules: `name` non-empty, `version` non-empty, `dataInput.type` must be `"file"`, `dataInput.accepts` non-empty array, `mcpTools` array.

### 3. Add Tool Handlers to app.js

Integrate directly in the app's main script — no separate bridge file needed. The platform auto-injects `window.__openyoghurt` via the `miniapp://` protocol.

**Schema format** (use per-sheet structure):

```javascript
{
  sheets: {
    "sheet-name": {
      columns: [
        { name: "col", type: "string", sampleValues: ["a", "b"], uniqueCount: 42 }
      ],
      rowCount: 100
    }
  },
  totalRows: 100
}
```

**Tool handler pattern:**

```javascript
// After data processing, notify the platform:
if (window.__openyoghurt && window.__openyoghurt.notifyDataLoaded) {
  window.__openyoghurt.notifyDataLoaded(schema);
}

// Tool handlers
window.__openyoghurt_handlers = {
  get_data_schema: async function() { return schema; },

  query_data: async function(params) {
    // Apply filters, grouping, aggregation
    return { data: rows, rowCount: rows.length, truncated: false };
  },

  get_computed_metrics: async function(params) {
    // Pre-computed KPIs — filters to relevant records automatically
    return { metric1: value, metric2: value };
  },

  get_sample_rows: async function(params) {
    var count = Math.min((params.count || 10), 100);
    return { rows: data.slice(0, count), totalRows: data.length };
  }
};

// Register data status
if (window.__openyoghurt) {
  window.__openyoghurt.isDataLoaded = function() { return data.length > 0; };
  window.__openyoghurt.getDataSchema = async function() { return schema; };
}
```

**Handler contract:** async functions, single `params` argument, return JSON-serializable results, never throw (return `{ error: "message" }` instead).

### 4. Declare MCP Tools in Manifest

Add tool declarations to `mcpTools`. Names must exactly match keys in `window.__openyoghurt_handlers`.

### 5. Write Detailed Tool Descriptions

**This is critical.** Tool descriptions are the agent's only guide to using data correctly. Vague descriptions cause wrong answers.

**Always include in raw query tool descriptions:**
- Whether data includes mixed record types (active+terminated, current+historical) and how to filter
- Date/period column formats (YYYY-MM-DD vs YYYY-MM)
- Whether numeric values are per-period (need summing for multi-month) or cumulative
- Whether rates are decimals (0.45) or percentages (45)
- Units for numeric fields (years, sqm, EUR, etc.)
- Non-obvious rows to exclude from aggregations (e.g., ground floors with zero desks)
- Recommendation to prefer computed KPI tools when available

**Always include in computed KPI tool descriptions:**
- What filtering it applies automatically (e.g., "only counts active employees")
- What each returned field means with units (e.g., "attritionRate: trailing 12-month percentage")
- Formulas for derived values (e.g., "shareRatio = assignedHC / totalDesks")
- Default behavior (e.g., "uses latest available period if none specified")
- "Prefer this over query_data for [type of question]"

**Example — weak vs strong:**

Weak: `"Query employee data with filters"`

Strong: `"Query employee data. IMPORTANT: includes both active and terminated employees. Filter by {column: 'termination_date', operator: 'is_blank'} for active only. For headcount KPIs, prefer get_workforce_metrics which handles this automatically."`

### 6. Verify

- `manifest.json` is valid JSON with all required fields
- Tool names in manifest match `window.__openyoghurt_handlers` keys exactly
- Schema uses `{ sheets: {}, totalRows }` format
- Tool descriptions cover data semantics (see step 5)
- Data loading calls `notifyDataLoaded(schema)` on success

## Reference Files

- Full guide: `docs/miniapp-conversion-guide.md`
- Manifest parser: `src/main/mini-app/manifest-parser.ts`
- Protocol handler: `src/main/mini-app/protocol.ts`
- Iframe bridge (auto-injected): `src/main/mini-app/iframe-bridge.ts`
- Tool routing: `src/main/claude/tool-handler.ts`
- Agent prompts: `src/main/claude/prompts.ts`
- Example apps: `examples/apps/human-resources/`, `campus-management/`, `finance-controlling/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sinishadjukic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
