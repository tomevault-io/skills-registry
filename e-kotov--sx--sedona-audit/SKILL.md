---
name: sedona-audit
description: Audit existing sx implementations against SedonaDB sources. Use when reviewing implementations after SedonaDB updates, verifying existing functions match current documentation, or identifying unimplemented functions. Use when this capability is needed.
metadata:
  author: e-kotov
---

# SedonaDB Implementation Audit Skill

Use this skill to verify that existing sx implementations correctly match current SedonaDB sources, or to identify functions that need updates after a SedonaDB version change.

## When to use

- After SedonaDB version updates
- When troubleshooting unexpected behavior
- For periodic quality review
- Before a new sx release
- To identify which functions are not yet implemented

## Audit workflow

### 1. Generate function inventory

#### List all SedonaDB functions

Read `.sync/sedona-db/rust/sedona-functions/src/register.rs` and extract all registered UDFs from:
- `register_scalar_udfs!` - Scalar functions
- `register_aggregate_udfs!` - Aggregate functions

#### List all sx implementations

Find all `R/sx_*.R` files and extract the function names.

#### Create inventory table

Create or update `.claude/research/sedona/_inventory.md`:

```markdown
# SedonaDB Function Inventory

Last updated: [date]
SedonaDB version: [check .sync/sedona-db]
sx version: [check DESCRIPTION]

## Summary
- Total SedonaDB functions: XX
- Implemented in sx: XX
- Not implemented: XX

## Implemented Functions

| SedonaDB Function | sx Function | File | Tests | Notes |
|-------------------|-------------|------|-------|-------|
| ST_Buffer | sx_buffer | R/sx_buffer.R | Yes | |
| ST_Centroid | sx_centroid | R/sx_centroid.R | Yes | |
| ST_Envelope | sx_envelope | R/sx_envelope.R | Yes | |
| ST_Simplify | sx_simplify | R/sx_simplify.R | Yes | |
| ST_Transform | sx_transform | R/sx_transform.R | Yes | |
| ST_Join predicates | sx_join | R/sx_join.R | Yes | |

## Not Implemented

| SedonaDB Function | sf Equivalent | Priority | Notes |
|-------------------|---------------|----------|-------|
| ST_ConvexHull | st_convex_hull | Medium | |
| ST_Difference | st_difference | Medium | Binary op |
| ST_Intersection | st_intersection | High | Binary op, common |
| ST_Union | st_union | High | Binary op, common |
| ST_Area | st_area | High | Returns numeric |
| ST_Length | st_length | High | Returns numeric |
| ST_Distance | st_distance | High | Returns numeric |
| ... | ... | ... | ... |

## Function Categories

### Constructors (create geometry)
- ST_Point, ST_MakeLine, ST_GeomFromWKT, ST_GeomFromWKB, etc.

### Accessors (extract properties)
- ST_X, ST_Y, ST_Z, ST_M, ST_SRID, ST_CRS, ST_AsText, ST_AsBinary, etc.

### Metrics (return numeric)
- ST_Area, ST_Length, ST_Perimeter, ST_Distance, etc.

### Predicates (return boolean)
- ST_Contains, ST_Within, ST_Intersects, ST_Disjoint, etc.

### Transformations (return geometry)
- ST_Buffer, ST_Centroid, ST_ConvexHull, ST_Simplify, etc.

### Overlay (binary geometry operations)
- ST_Union, ST_Intersection, ST_Difference, ST_SymDifference

### Aggregates (multiple rows to one)
- ST_Union_Agg, ST_Collect_Agg, ST_Envelope_Agg, etc.
```

### 2. Individual function audit

For each implemented function, verify:

#### A. SQL signature match
- [ ] All documented arguments are supported
- [ ] Argument types match (Geometry vs Geography)
- [ ] Return type is correct
- [ ] `Since` version is noted (if changed recently)

#### B. Rust implementation match
- [ ] ArgMatcher types are correctly handled
- [ ] Optional arguments have correct defaults
- [ ] Volatility is appropriate

#### C. Test coverage
- [ ] Unit tests exist (`test-sx_{name}.R`)
- [ ] sf comparison tests exist (`test-sx_vs_sf_{name}.R`)
- [ ] Edge cases from Python tests are covered

#### D. Documentation accuracy
- [ ] Description matches SQL docs
- [ ] `@seealso` link is correct and working
- [ ] sf-specific ignored args are documented

### 3. Generate audit report

Create or update `.claude/research/sedona/_audit_report.md`:

```markdown
# sx SedonaDB Audit Report

Generated: [date]
SedonaDB Version: [from .sync/sedona-db]
sx Version: [from DESCRIPTION]

## Summary
- Functions audited: XX
- Needing updates: XX
- Not implemented: XX
- All tests passing: Yes/No

## Functions Needing Attention

### Updates Required

| Function | Issue | Priority | Action |
|----------|-------|----------|--------|
| sx_buffer | Missing new param from v0.3 | High | Add buffer_style param |

### Missing Tests

| Function | Issue | Priority |
|----------|-------|----------|
| sx_simplify | No sf comparison test | Medium |

### Documentation Issues

| Function | Issue | Priority |
|----------|-------|----------|
| sx_centroid | @seealso link broken | Low |

## Detailed Findings

### sx_buffer
- **Status:** Needs update
- **Issue:** SedonaDB v0.3 added `buffer_style` parameter
- **Current signature:** `ST_Buffer(geom, distance)`
- **New signature:** `ST_Buffer(geom, distance, buffer_style?)`
- **Action:** Add optional `buffer_style` parameter
- **Priority:** High

### sx_centroid
- **Status:** OK
- **Last verified:** [date]
- **Notes:** Matches current SedonaDB implementation

### sx_envelope
- **Status:** OK
- **Last verified:** [date]

[Continue for each function...]

## Recommendations

### High Priority
1. Update sx_buffer to support new parameters
2. Implement ST_Intersection (commonly used)
3. Implement ST_Area (basic accessor)

### Medium Priority
1. Add missing sf comparison tests
2. Implement ST_ConvexHull
3. Implement ST_Union

### Low Priority
1. Fix documentation links
2. Implement less common functions
```

### 4. Prioritize findings

Use this priority framework:

| Priority | Criteria |
|----------|----------|
| **High** | Function behavior changed, security issues, broken functionality, commonly used function missing |
| **Medium** | New optional parameters, documentation updates, moderately used function missing |
| **Low** | Minor improvements, style consistency, rarely used function missing |

## After audit

For functions needing updates:
1. Re-run `sedona-research` skill to get current state
2. Update implementation using findings
3. Update tests
4. Re-audit to confirm

For unimplemented functions:
1. Run `sedona-research` skill
2. Run `sedona-implement` skill
3. Add to inventory

## Checklist

- [ ] Generated function inventory from `register.rs`
- [ ] Listed all `sx_*` implementations
- [ ] Created/updated inventory at `.claude/research/sedona/_inventory.md`
- [ ] Compared all implementations against current sources
- [ ] Identified functions needing updates
- [ ] Identified unimplemented functions
- [ ] Created audit report at `.claude/research/sedona/_audit_report.md`
- [ ] Prioritized findings
- [ ] Created issues/tasks for high-priority items

## Quick commands

```bash
# Find all registered SedonaDB functions
grep -E "crate::" .sync/sedona-db/rust/sedona-functions/src/register.rs | wc -l

# Find all sx implementations
ls R/sx_*.R | wc -l

# Run all sx tests
Rscript -e "devtools::test(filter = '^sx_')"

# Check for missing test files
for f in R/sx_*.R; do
  name=$(basename "$f" .R)
  if [ ! -f "tests/testthat/test-$name.R" ]; then
    echo "Missing tests: $name"
  fi
done
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/e-kotov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
