---
name: connector-skill
description: > Use when this capability is needed.
metadata:
  author: jeremyserna-lgtm
---

# Data Connector Skill

## Query Patterns

### SQL-based Sources
Write dialect-appropriate SQL. Check schema first, validate results after.

### API-based Sources
Use MCP tools to query. Handle pagination and rate limits.

### File-based Sources
Read CSV/Excel/JSON files and analyze with pandas or similar.

## Validation Checklist
- Row count sanity
- Null check
- Value range check
- Aggregation logic

## Trace Awareness
When previous TRACE.md or FILTER.md files exist for this data source:
1. Read the Attention Log — know what schema areas were already explored
2. Read the Confidence Map — know where schema understanding is weak
3. Read the Surplus Value — know what data patterns emerged last time
4. Read the Filter — skip known-noise areas, prioritize signal areas

## References
- See `references/schema-guide.md` for schema exploration patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremyserna-lgtm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
