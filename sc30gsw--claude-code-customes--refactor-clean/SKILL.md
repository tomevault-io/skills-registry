---
name: refactor-clean
description: Safely identifies and removes dead code with test verification and rollback capabilities Use when this capability is needed.
metadata:
  author: sc30gsw
---

# Refactor Clean

Safely identify and remove dead code with test verification:

1. Run dead code analysis tools:
   - knip: Find unused exports and files
   - depcheck: Find unused dependencies
   - ts-prune: Find unused TypeScript exports

2. Generate comprehensive report in .reports/dead-code-analysis.md

3. Categorize findings by severity:
   - SAFE: Test files, unused utilities
   - CAUTION: API routes, components
   - DANGER: Config files, main entry points

4. Propose safe deletions only

5. Before each deletion:
   - Run full test suite
   - Verify tests pass
   - Apply change
   - Re-run tests
   - Rollback if tests fail

6. Show summary of cleaned items

Never delete code without running tests first!

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sc30gsw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
