---
name: knip
description: Finds unused dependencies, files, and exports in JS/TS projects. Use when cleaning up dead code, removing stale packages from package.json, or identifying unreferenced exports. Use when this capability is needed.
metadata:
  author: neversight
---

# Knip

Finds unused files, dependencies, and exports in TypeScript/JavaScript projects.

## Usage

```bash
bunx knip                     # Analyze project
bunx knip --production        # Production only (no tests, devDeps)
bunx knip --strict            # Direct dependencies only
bunx knip --fix               # Auto-remove unused (use cautiously)
bunx knip --include files     # Only unused files
bunx knip --include exports   # Only unused exports
bunx knip --include dependencies  # Only unused deps
```

## Output Formats

```bash
bunx knip --reporter compact  # Compact output
bunx knip --reporter json     # JSON for tooling
bunx knip --reporter github-actions  # CI annotations
```

## Filtering

```bash
bunx knip --workspace packages/client  # Specific workspace
bunx knip --exclude "test/**/*"        # Exclude patterns
```

## Debugging

```bash
bunx knip --debug                      # Debug output
bunx knip --trace-file src/utils.ts    # Trace file
bunx knip --trace-export myFunction    # Trace export
```

## Configuration

Configure via `.knip.json` or `knip.config.js` for custom entry points and exclusions.

## Related Skills

- **maintenance**: Refactoring and technical debt management
- **jscpd**: Find duplicate code blocks
- **bun**: Package management for JS/TS projects

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
