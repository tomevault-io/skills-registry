---
name: slop-detector
description: Analyze repository architecture to identify structural slop in TypeScript/JavaScript codebases. Use when reviewing folder structure, finding over-engineering, reducing complexity, or cleaning up project organization. Detects excessive directory depth, single-file folders, barrel files, empty directories, enterprise layer smell, mirrored structures, and scattered types/utils. Triggers on requests to analyze architecture, review project structure, find layering issues, or simplify codebase organization. Use when this capability is needed.
metadata:
  author: spatariurares
---

# Slop Detector

Analyze repository architecture to identify structural slop: excessive layering, fragmentation, and organizational overhead.

## Workflow

### 1. Run Architecture Scan

```bash
bash /path/to/skill/scripts/analyze-slop.sh <repository-path> [output-dir]
```

- Default output: `./docs/slop-report-YYYYMMDD-HHMMSS.md`
- Creates `docs/` folder if missing

### 2. Patterns Detected

| Pattern                     | Problem                       | Fix                    |
| --------------------------- | ----------------------------- | ---------------------- |
| 🪆 Deep nesting (5+ levels) | Hard to navigate              | Flatten structure      |
| 📁 Single-file directories  | Unnecessary fragmentation     | Move file up or merge  |
| 🛢️ Barrel files (index.ts)  | Circular deps, bundle bloat   | Direct imports         |
| 🕳️ Empty directories        | Dead code                     | Remove                 |
| 🏢 Enterprise layers        | Passthrough overhead          | Consolidate or justify |
| 🪞 Mirrored structures      | Same entity in many places    | Colocate by feature    |
| 📝 Centralized types        | Scattered from implementation | Colocate with code     |
| 🧰 Utils/helpers folders    | Dumping grounds               | Move to consumers      |

### 3. Review Checklist

For each flagged item, ask:

1. **Layers:** What value does this layer add? (validation, caching, logging, transformation)
2. **Directories:** Could this folder be flattened or merged?
3. **Separation:** Should these files be colocated by feature instead?

## Pattern Reference

See `references/slop-patterns.md` for:

- Architecture anti-patterns with examples
- Feature-based vs layer-based organization
- When layering is justified

## Output

Report saved to `docs/slop-report-YYYYMMDD-HHMMSS.md` with:

- Issues by category
- Summary metrics (dirs, files, depth)
- Actionable recommendations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spatariurares) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
