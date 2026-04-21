---
name: pr-reviewers
description: Recommend PR reviewers based on code ownership. Triggers: 'who should review', 'add reviewers', 'find reviewers'. Spreads load to avoid always picking same people. Use when this capability is needed.
metadata:
  author: luan
---

# PR Reviewer Picker

Recommend reviewers based on code expertise while spreading review load.

Rotating reviewers prevents knowledge silos and balances workload — the same reviewer on every PR misses patterns fresh eyes catch and creates bottlenecks when that person is unavailable. Recent-reviewer overlap is penalized, not rewarded.

## Steps

1. **Detect PR**: `gh pr view --json number,headRefName,author -q '.number'`
2. **Get changed files**: `gh pr view <PR> --json files -q '.files[].path'`
   - Separate **existing** vs **new** files (additions where `deletions == 0` and `status == "added"`)
   - **Exclude generated files**: `*.generated.*`, `*.pb.go`, `*.pb.swift`, `*_generated.rs`, `*.g.dart`, lock files, `vendor/`, `node_modules/`, `*.min.*`, `*.snap`, `__snapshots__/`. Also check for `@generated` marker in first 5 lines. Generated files skew blame toward whoever ran the generator.

3. **Gather candidates** (parallel): Read `${CLAUDE_SKILL_DIR}/references/scoring.md` for candidate gathering commands, scoring weights, and penalty multipliers.

4. **Validate**: Remove PR author, non-members, bots

5. **Score**: Apply scoring algorithm from `${CLAUDE_SKILL_DIR}/references/scoring.md`.

6. **Present results** — up to 3 candidates (don't pad if fewer exist). For each:
   - Score breakdown with concrete numbers
   - Expertise reason: "Owns 60% of changed lines", "12 commits to these files", "CODEOWNERS for src/auth/"
   - If penalized: "(currently reviewing 6 PRs)"

   **Never say** "Reviewed your recent PRs" as positive signal.

   `--auto` → add recommended reviewers directly. Without `--auto` → ask: "Add these reviewers?"

7. **Add**: `gh pr edit <PR> --add-reviewer alice,bob,carol`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
