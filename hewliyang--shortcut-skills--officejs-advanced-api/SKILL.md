---
name: officejs-advanced-api
description: Access Office.js Excel JavaScript API for Excel Add-in development. Use when you need to interact with Excel workbooks, worksheets, ranges, tables, charts, pivot tables, and other Excel objects in the Office Add-in environment. Use when this capability is needed.
metadata:
  author: hewliyang
---

# Office.js Advanced API

## Raw Objects

Access the underlying Excel objects:

```typescript
await Excel.run(async (context) => {
    const workbook = context.workbook;                    // Excel.Workbook
    const sheet = workbook.worksheets.getActiveWorksheet(); // Excel.Worksheet
    const range = sheet.getRange("A1:B10");               // Excel.Range

    // Load properties before reading
    range.load("values, formulas, format");
    await context.sync();

    // Now you can access loaded properties
    console.log(range.values);
});
```

## API Reference Structure

The type definitions in `api-reference.ts` contain the `Excel` namespace (~69K lines):

```
Excel
├── Application           (line ~10410) - App-level settings, calculation mode
├── Workbook              (line ~10658) - Workbook operations, protection
├── Worksheet             (line ~11188) - Sheet operations, visibility, freeze panes
├── WorksheetCollection   (line ~11804) - Add/get/delete sheets
├── Range                 (line ~12423) - Cell operations, values, formats, formulas
├── RangeAreas            (line ~13608) - Non-contiguous ranges
├── NamedItem             (line ~14977) - Named ranges/formulas
├── Table                 (line ~15632) - Structured tables
├── TableCollection       (line ~15447) - Add/get tables
├── RangeFormat           (line ~16713) - Cell formatting
├── RangeFill             (line ~16962) - Background fill
├── RangeFont             (line ~17208) - Font styling
├── Chart                 (line ~17466) - Charts and chart elements
├── ChartCollection       (line ~17329) - Add/get charts
├── PivotTable            (line ~23316) - Pivot tables
├── ConditionalFormat     (line ~25586) - Conditional formatting rules
├── Comment               (line ~29418) - Cell comments/notes
├── Shape                 (line ~29926) - Shapes, images, icons
└── Slicer                (line ~31264) - Table/PivotTable slicers
```

## Searching the API

```bash
# Find a class and its methods
grep -n "class Range extends" api-reference.ts -A 300 | head -150

# Search for method names
grep -n "getUsedRange\|getEntireColumn\|getEntireRow" api-reference.ts

# Find enums and their values
grep -n "enum [A-Z]" api-reference.ts | head -50
grep -n "enum HorizontalAlignment" api-reference.ts -A 20

# Search by keyword
grep -in "conditional\|format" api-reference.ts | head -50

# Find interfaces for options/config objects
grep -n "interface.*Options\|interface.*Data" api-reference.ts | head -30
```

## Cautions

1. **Sync is required** - Changes aren't applied until `context.sync()` is called
2. **Load before read** - Properties must be loaded before accessing them
3. **Proxy objects** - Excel objects are proxies; don't cache them outside `Excel.run`
4. **API Sets** - Check `[Api set: ExcelApi X.X]` comments for version requirements

## Reference

- Type definitions: `/skills/default/officejs-advanced-api/api-reference.ts`
- Online docs: https://learn.microsoft.com/en-us/javascript/api/excel

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hewliyang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
