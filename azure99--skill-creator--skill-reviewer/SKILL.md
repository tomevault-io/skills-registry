---
name: skill-reviewer
description: >- Use when this capability is needed.
metadata:
  author: azure99
---

# Skill Reviewer

Review a given skill folder against the `skill-creator` standards and produce an actionable report (with severities and specific fixes).

## Quick start

1. Get the skill folder path (or the full `SKILL.md` plus a directory tree).
2. Run the automated lint (when you have filesystem access):
   - From the `skill-reviewer` folder: `python scripts/lint_skill.py /path/to/skill`
3. Review manually using the checklist and standards references.
4. Produce the review report using `references/report-template.md`.
5. If fixing, follow `references/remediation.md`, re-run lint, and update the report.

## Workflow

### 0) Intake (ask first, keep it lightweight)

- Ask for:
  - Skill folder path
  - Intended purpose and the top 3–5 example user prompts that should trigger it
  - Any constraints (offline/no-network, no deletions, target runtime, etc.)
  - Variants (providers/frameworks/domains)
- Confirm scope (review only vs review + fix).

### 1) Lint first (blockers)

- Run `python scripts/lint_skill.py <skill_dir>` and treat errors as blockers.
- Re-run with `--strict` when you want warnings to fail CI-like checks.

### 2) Manual review (follow checklist)

- Execute `references/checklist.md` top-to-bottom.
- Use `references/standards.md` when you need to map “skill-creator rules” to concrete criteria.

### 3) Packaging readiness (when applicable)

- If a packager/validator exists in the environment, run it and treat failures as blockers.
  - Example: `python skills/skill-creator/scripts/package_skill.py <path/to/skill>`
- Otherwise rely on the automated lint plus the manual checklist.

### 4) Fixes (only when requested)

- Apply minimal, targeted changes.
- Re-run lint/validation after fixes.
- Update the report sections: Findings, Fixes Applied, Remaining Issues, Tests / Validation.

## Guardrails

- Use imperative/infinitive voice in edits.
- Keep `SKILL.md` under ~500 lines; move deep/variant details to `references/`.
- Keep references one level deep; add a TOC for files over 100 lines.
- Do not add auxiliary docs (README, CHANGELOG, QUICK_REFERENCE, etc.).
- If a fix could delete or overwrite user content, confirm or explain before doing it.

## Bundled files

- `scripts/lint_skill.py`: Automated lint for structure/frontmatter/reference hygiene.
- `references/checklist.md`: The step-by-step review checklist (with severities).
- `references/standards.md`: The condensed `skill-creator` standards to enforce.
- `references/report-template.md`: The report structure to use.
- `references/remediation.md`: Common fixes and rewrite patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/azure99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
