---
name: spec
description: Create a new specification document from a user prompt Use when this capability is needed.
metadata:
  author: jerichobob
---

# Create a New Spec

Create a new specification document based on the user's prompt: $ARGUMENTS

## Pre-parsed Data

### Existing Specs

!`${CLAUDE_PLUGIN_ROOT}/scripts/specs-parse.sh spec-list`

## Instructions

1. **Determine the version number**: Using the spec-list data above, find the highest version number and increment by 1. If no specs exist, start at v1.

2. **Find the template**: Read `specs/TEMPLATE.md` if it exists. If not, use the canonical template at `${CLAUDE_PLUGIN_ROOT}/templates/TEMPLATE.md`.

3. **Create a short name**: Based on the user's prompt (`$ARGUMENTS`), create a kebab-case name (e.g., "user-authentication", "dark-mode"). The filename should be: `spec-v{N}-{short-name}.md`

4. **Draft the spec**:
   - Parse `$ARGUMENTS` to identify the WHY (problem/goal), WHAT (requirements), and HOW (approach)
   - Fill in the template with reasonable defaults
   - Mark all checklist items as unchecked `- [ ]`
   - Set status to "Draft"
   - Set the date to today

5. **Write the file**: Save to `specs/spec-v{N}-{short-name}.md`

6. **Update README**: Add the new spec to `specs/README.md`:
   - Add a Quick Status table row
   - Add a new `## v{N}: {Name}` section with the Phase/Task checklists

7. **Report back**: Tell the user the spec was created and show the filename.

## Example

If the user runs `/sdd:spec add ability to export classifications to CSV`, create:

- File: `specs/spec-v5-csv-export.md`
- Why: "As a user, I want to export classifications to CSV so that I can analyze data in spreadsheets"
- What: Requirements for CSV export feature
- How: Phased implementation plan

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jerichobob) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
