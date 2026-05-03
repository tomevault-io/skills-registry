---
name: vault-manage-project-notes
description: Create and update project notes in 05-Projects, including project descriptions, summaries, scope, and next actions. Use when asked to document project status, add implementation summaries, or maintain project pages. Use when this capability is needed.
metadata:
  author: danieljhkim
---

# Vault Manage Project Notes

Manage project records under `05-Projects` with consistent structure and concise status updates.

## Workflow

1. Resolve project title and target file.
2. Create project note if missing.
3. Update description/summary and action items.
4. Validate and return final path.

## Create Project Note

- Prefer `create_note` with `note_type=project`.
- Include frontmatter fields expected by schema:
- `type`, `created`, `status`, `title`.
- If templates are missing and creation fails, create the markdown file directly in `05-Projects` with valid frontmatter.

## Update Existing Project Note

- Use `update` for full-body rewrite when requested.
- Use `append_section` for incremental additions.
- Keep sections in this order:
- `## Objective`
- `## Scope`
- `## Milestones`
- `## Next Actions`

## Summary Pattern

- For implementation summaries include:
- What was implemented.
- What was validated.
- Remaining risks or follow-ups.

## Validation And Policy

- Run `validate_note` after changes.
- If policy blocks mutation (`read_only` or `immutable`), stop and report the blocked path.

## Output Format

- Return:
- Final project note path in `05-Projects`.
- One paragraph summary of current implementation status.
- Next actions list (max 5 items).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danieljhkim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
