---
name: skills-improve
description: Analyze a skill for improvement opportunities, implement selected ones, optimize for conciseness, and review. Use when "improve this skill", "make this skill better", "optimize skill". Use when this capability is needed.
metadata:
  author: lucadellanna
---

Improve a skill through analysis, targeted changes, and optimization. Supports both project skills (a `./skills/` directory) and installed skills.

Use the minimal framework and requirements checklist in this skill's `criteria.md` (same folder). If `framework/REQUIREMENTS.md` and `framework/FRAMEWORK.md` are available in the current project, read them too.

---

## Phase 1 — Brainstorm

### 1. Identify target

Determine which skill to improve and where it lives (project vs installed).

- If the user names one, use it.
- Otherwise, scan for skills and ask which one:
  - If `./skills/` exists in the current project/workspace, scan that.
  - Otherwise, scan installed skills in `~/.claude/skills/`.
- Ignore machine-managed folders like `.backups/` when scanning.
- Do not target `skills-improve` itself.

### 2. Read and analyze

Read the skill's folder: `SKILL.md`, and any `criteria.md`, `template.md`, `pending-updates.md`, or other relevant files present. Ignore machine-managed artifacts like `.backups/` and `.skillstate.json` unless they are directly relevant to the user's request.

Assess against the improvement dimensions in this skill's `criteria.md` (same folder). For each dimension, generate specific, actionable suggestions tied to what you actually read — not generic advice. Reference specific steps or sections.

### 3. Present suggestions

If `AskUserQuestion` is available, use it with `multiSelect: true`. Otherwise, present suggestions conversationally and let the user respond in plain language.

- Number each suggestion.
- For each: what to change, why it matters, which file(s) it touches.
- Group by dimension.

The user may select improvements, ask follow-up questions, or add their own ideas. If the user selects none, skip to Phase 3 (Optimize).

---

## Phase 2 — Implement

### 4. Preview

For each selected improvement, show what will change:
- Which file(s) will be modified or created.
- A plain-language summary of the change.

Proceed only after the user confirms.

### 5. Backup

Before modifying files, create a timestamped backup:
- Create `{skill_folder}/.backups/{filesystem-safe-timestamp}/` (use an ISO-8601 timestamp with `:` replaced by `.`).
- Record a list of the files that exist before changes (exclude `.backups/`) so rollback can remove newly created files.
- Copy all files that will change into the backup folder.

### 6. Apply selected improvements

- Preserve existing structure and voice.
- If an improvement requires a new file (e.g. extracting criteria into `criteria.md`), create it.
- Update `last-updated` in SKILL.md frontmatter to current UTC timestamp (ISO-8601).

### 7. Verify

Check that the modified skill is still well-formed:
- SKILL.md frontmatter is valid YAML.
- All files that existed before still exist.
- No broken references to other files or skills.

### 8. Rollback if needed

If verification fails:
- Restore all files from the backup created in step 5.
- Delete any newly created files that were not present in the pre-change file list (excluding `.backups/` itself).
- Inform the user: "The improvements didn't apply cleanly. I've restored the skill to its previous state."
- Skip to step 13 (Summary) to report what went wrong. Do not run /skills-review in this case.

---

## Phase 3 — Optimize

### 9. Optimize for conciseness

Re-read the improved skill. Assess against the optimization checklist in this skill's `criteria.md`.

### 10. Present optimization proposals

Show the user what you would tighten, with before/after for significant changes.

Ask for confirmation. If declined, skip to Phase 4.

### 11. Apply optimizations

Apply confirmed optimizations. Update `last-updated`.

---

## Phase 4 — Review

### 12. Run skills-review

Invoke `/skills-review` on the improved skill.

### 13. Summary

Report:
- What was improved and why.
- Approximate token count change (if optimization was applied).
- Any review findings that need attention.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lucadellanna) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
