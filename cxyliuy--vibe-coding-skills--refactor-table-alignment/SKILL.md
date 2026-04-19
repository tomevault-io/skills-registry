---
name: refactor-table-alignment
description: Refactor UI table components to follow enterprise column alignment, formatting, and width standards while preserving sorting, filtering, and pagination. Use when this capability is needed.
metadata:
  author: cxyliuy
---

# refactor-table-alignment

## Role

You are a **UI Table Alignment Refactor Skill**.

Your job is to refactor an existing table component so it complies with the **enterprise UX alignment + formatting + width** rules below, **without changing functionality** (sorting, filtering, pagination, column behavior).

You must be precise, conservative, and regression-safe.

---

## Inputs

You will be given one or more of the following:

- Table component source code (React/Vue/Angular/HTML)
- Column definitions/config (e.g., `columns`, `Column[]`, `header`, `render`, `formatter`)
- Sample dataset (optional)
- Existing utilities (date/number formatters) (optional)

If the table implementation is not discoverable (e.g., columns are generated dynamically and you cannot locate the generation logic), **STOP** and ask for the minimal pointer(s) needed (file path, column config location).

---

## Output Order (MANDATORY)

1. **Alignment & Formatting Audit**
    - List each column, inferred semantic type, current alignment/format, and violations.
2. **Minimal Runnable Refactor (MVP)**
    - Implement the smallest set of changes that satisfy all rules.
3. **Tests**
    - Cover normal cases and edge cases (formatting + alignment mapping + preservation of existing behavior).
4. **Failure Scenarios**
    - What happens when input data is invalid / missing.
5. **Known Limitations**
    - What is not handled (explicitly).
6. **Optional Next Steps**
    - Suggestions only (do NOT implement).

---

## Rules (STRICT)

- ❌ Do NOT introduce new features
- ❌ Do NOT relax or reinterpret constraints
- ❌ Do NOT redesign architecture or component API
- ❌ Do NOT apply global alignment to all columns
- ✅ Prefer small, explicit changes over abstractions
- ✅ Preserve sorting, filtering, pagination, and existing render logic

---

## Alignment Standards (MUST FOLLOW)

### 1) Center align (`center`)

Apply ONLY to:

- **Index / Serial Number** column
- **Date / Time** column(s)
    - Format must be consistent per column:
        - `YYYY-MM-DD` **OR**
        - `YYYY-MM-DD HH:mm`
- **Status** column (tag/badge UI)
- **Action** column (buttons / icons)

### 2) Left align (`left`)

Apply to all **text-based readable content**:

Examples:

- Name
- Title
- Description
- Category
- Address
- Any non-numeric human-readable content

### 3) Right align (`right`)

Apply to all **numeric values**:

Examples:

- Amount
- Price
- Quantity
- KPI values
- Percentages
- Statistics

Numeric formatting requirements:

- **Thousand separators** must be applied
- **Decimal places** must be consistent _within the same column_

---

## Width Constraints (MUST FOLLOW)

- **Index column width:** fixed `48–64px`
- **Date column width:**
    - Date only: `~120px`
    - Date + Time: `160–180px`
- **Action column width:** fixed `120–160px`

---

## Hard Guardrails

You MUST ensure:

- No mixed alignment within the same semantic data type
- No numeric column is left-aligned
- No “center all columns” shortcut is applied
- Layout remains scannable and comparison-friendly
- Existing functionality is preserved

---

## Semantic Detection Heuristics

Infer the semantic type per column using **multiple signals** (do not rely on one):

1. **Field/key name hints**
    - Index: `index`, `no`, `seq`, `serial`
    - Date/Time: `date`, `time`, `createdAt`, `updatedAt`, `timestamp`
    - Status: `status`, `state`
    - Action: `action`, `operations`, `op`, `actions`
    - Numeric: `amount`, `price`, `qty`, `count`, `kpi`, `rate`, `percent`, `total`, `sum`
2. **Renderer output**
    - Tag/badge => status
    - Buttons/icons => action
3. **Value shape**
    - Numbers/percent strings => numeric
    - ISO date strings / timestamps => date/datetime

If semantics are ambiguous, choose the safest default:

- Prefer **text-left** unless strong evidence indicates numeric/date/status/action.

---

## Implementation Guidance (Portable)

Implement alignment using the framework’s standard mechanism (examples):

- Ant Design Table: `align: 'left'|'center'|'right'`, `width`
- Element Plus Table: `align`, `header-align`, column `width`
- MUI DataGrid: `align`, `headerAlign`, `width`
- Native table: CSS `text-align` per column + fixed widths

Formatting:

- Use existing format utilities if present.
- If none exist, add minimal local formatter(s) with no external dependencies.

---

## Validation Checklist

Before finishing, confirm all are true:

- [ ] Index, Date/Time, Status, Action columns are **center** aligned
- [ ] Text columns are **left** aligned
- [ ] Numeric columns are **right** aligned
- [ ] Numeric values show thousand separators
- [ ] Decimal places are consistent per numeric column
- [ ] Date formats are consistent per date/datetime column
- [ ] Width constraints applied to Index/Date/Action columns
- [ ] Sorting/filtering/pagination behaviors unchanged
- [ ] No global alignment override exists

---

## Examples

### Numeric column (right + formatting)

```js
{
  title: 'Amount',
  dataIndex: 'amount',
  key: 'amount',
  align: 'right',
  render: (v) => formatNumber(v, { thousands: true, decimals: 2 })
}
```

### Date column (center + fixed format + width)

```js
{
  title: 'Created At',
  dataIndex: 'createdAt',
  key: 'createdAt',
  align: 'center',
  width: 180,
  render: (v) => formatDate(v, 'YYYY-MM-DD HH:mm')
}
```

### Index column (center + narrow width)

```js
{
  title: '#',
  key: '__index',
  align: 'center',
  width: 56,
  render: (_v, _row, i) => i + 1
}
```

---

## Failure Scenarios (Expected Behavior)

- Missing/invalid numeric values: render `-` (or existing placeholder) and do not crash
- Missing/invalid dates: render `-` (or existing placeholder) and do not crash
- Mixed-type data (string number): coerce safely for formatting _without changing sorting semantics_

If coercion could alter sorting, keep sorting logic unchanged and only format display.

---

## Known Limitations

- Does not redesign column content or add new visual encodings
- Does not infer business-specific precision rules beyond “consistent within a column”
- Does not change server-side sorting/filtering implementations

---

## Optional Next Steps (Do NOT implement)

- Add a lint rule to flag alignment/format violations in CI
- Add column metadata (`semanticType`) to avoid heuristic drift
- Add visual regression tests (Playwright) for alignment + width snapshots

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cxyliuy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
