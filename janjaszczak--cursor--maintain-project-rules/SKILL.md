---
name: maintain-project-rules
description: Audit and maintain project rules in .cursor/rules/. Use when auditing project rules, checking prefix convention, syncing doc/rules.md, or when the user asks about .cursor/rules or prefix convention. Use when this capability is needed.
metadata:
  author: janjaszczak
---

# maintain-project-rules

## Activation gate

Use if:
- User asks to audit or maintain project rules, check prefix convention, or sync doc/rules.md.
- Context involves `.cursor/rules/`, rule files (`.mdc`), or conventions from cursor-rules-principles / retro.

## Source of truth

- **cursor-rules-principles.md** (in repo or ~/.cursor) – prefix table 000–500, general principles. See [reference.md](reference.md) for full prefix table.
- **commands/retro.md** (PROJECT RULES section) – prefixes 000/050/100/200/900, flat .mdc, no subfolders in rules.

## Procedure

1. **Resolve paths**
   - PROJECT_ROOT: `git rev-parse --show-toplevel`
   - PROJECT_CURSOR_DIR: `${PROJECT_ROOT}/.cursor`
   - PROJECT_RULES_DIR: `${PROJECT_CURSOR_DIR}/rules`
   - If PROJECT_RULES_DIR does not exist, report "No .cursor/rules in this repo" and stop.

2. **Audit**
   - List all `*.mdc` in PROJECT_RULES_DIR (flat; no subfolders per convention).
   - For each file: name, prefix vs principles/retro, frontmatter (`description`, `alwaysApply`, `globs`), line count (~&lt;50 preferred), overlap with other rules (duplicate or near-duplicate content).
   - Flag: missing prefix, wrong prefix slot, missing/invalid frontmatter, oversized file, duplicate content.

3. **Proposals**
   - RENAME: file without numeric prefix → suggest prefix from reference.md / retro.
   - MERGE: two or more rules with overlapping scope → suggest target file and sections.
   - DELETE: only with clear justification (obsolete, fully merged); do not execute without user confirmation.
   - Do not delete, move, or rename until user confirms.

4. **Sync doc/rules.md**
   - If repo has `doc/rules.md` or `docs/rules.md`: update the "Context-Specific Rules" (or equivalent) list to match current `.mdc` files and their globs/scope. Keep doc and rules directory in sync.

## Output

- **Report:** table of files (path, prefix OK?, frontmatter OK?, lines, notes); list of proposals (RENAME/MERGE/DELETE with reason and target).
- **Closing line:** "Do zastosowania: potwierdź zmiany lub wywołaj agenta rules-keeper."
- No destructive changes without explicit user confirmation.

## Reference

For full prefix table and conventions, see [reference.md](reference.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/janjaszczak) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
