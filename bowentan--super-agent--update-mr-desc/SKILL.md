---
name: update-mr-desc
description: Updates merge request descriptions in GitLab by summarizing MR changes into a bulleted list, excluding changes under docs/. Use when updating an MR description via glab MCP or glab CLI, or when the user asks to summarize MR changes for the description. Use when this capability is needed.
metadata:
  author: bowentan
---

# Update MR Description

Update a merge request's description by inspecting its changes and writing a concise bullet summary. Prefer **glab MCP**; use **glab CLI** when MCP is unavailable.

---

## Workflow

1. **Get the MR diff**.
2. **Filter out docs:** Ignore all changes under the `docs/` path under the project root.
3. **Summarize** the remaining changes into a short bullet list (by intent/feature, not a raw file list).
4. **Show and confirm:** Present the proposed description to the user and wait for explicit confirmation before writing to the remote.
5. **Update the MR description** with that list (only after confirmation).

---

## Getting the diff

- **MCP:** Use available tools for the current or specified MR to get the patch.
- **CLI:** From the MR branch or repo: `glab mr diff [MR_ID]` (omit MR_ID when one MR is in context).

---

## Excluding docs

When building the summary, **do not include** any change whose path:

- `docs/` under project root

Only summarize changes outside those paths.

---

## Summary format

Write 3-8 bullets that describe **what changed and why**, not just file names.

**Good:**
- Add login endpoint and JWT validation
- Extend task model with priority field and migration
- Fix date formatting in report export

**Bad:**
- Changed `src/auth.py`
- Changed `src/models/task.py`
- Changed `reports.py`

Infer intent from diffs (new functions, new fields, renames, fixes) and phrase bullets accordingly.

---

## Updating the description

**Always show the proposed description and get user confirmation before writing to the remote.** Do not call the update tool or CLI until the user has approved the text.

- **MCP:** Use the MR update tool with the `description` (or equivalent) field set to the new content. Preserve or replace existing description per user preference; if unclear, replace with the new bullet summary.
- **CLI:** `glab mr update [MR_ID] --description "$(cat <<'EOF'
- Bullet one
- Bullet two
EOF
)"`
  Escape or quote the description so newlines and bullets are preserved.

---

## Checklist

- [ ] Diff retrieved (MCP or CLI)
- [ ] All changes under `docs/` excluded from summary
- [ ] Summary is bullet and describes intent, not just paths
- [ ] Proposed description shown to user and confirmation received
- [ ] MR description updated via MCP or `glab mr update` (only after confirmation)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bowentan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
