---
name: run-quality-gates
description: Run lint, typecheck, and tests, then summarize results with remediation guidance. Use when this capability is needed.
metadata:
  author: roninrage
---

# Run Quality Gates Skill

## Workflow
1. Ask approval to run terminal commands.
2. Execute sequentially in project root or app folder:
   - `npm run lint`
   - `npm run typecheck`
   - `npm test`
3. Collect outcomes (pass/fail) for each gate.
4. Summarize results and provide targeted remediation steps.

## Interpreting Failures
- Lint failures: show rule name and offending file; suggest quick fixes (e.g., quotes, unused vars).
- Typecheck failures: show file and TypeScript error; recommend typing or config fixes.
- Test failures: show failing test name and assertion; propose test or code adjustments respecting boundaries.

## Sample Output Format
```
Quality Gates Summary
- Lint: PASS
- Typecheck: FAIL (src/components/Foo.tsx: TS1234 ...)
- Tests: PASS (12/12)

Next Steps
- Fix TS1234 in src/components/Foo.tsx by narrowing type of prop `bar`.
- Re-run: npm run typecheck
```

## Notes
- Cross-platform commands; no external binaries.
- Stream diffs and ask approval before running terminals.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/roninrage) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
