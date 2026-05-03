---
name: plan-doc
description: Create a plan document in the Obsidian vault before starting implementation Use when this capability is needed.
metadata:
  author: pindamster
---

Create a plan document in the Obsidian vault for: $ARGUMENTS

## Target Detection
Same as /explain: check $ARGUMENTS for project slug, then classify based on **what the work is about** (not just the working directory). Vault/skills/tooling work -> `general`. Project-specific work -> detect from working directory. Fallback to general.

## Steps
1. Read the plan template at `~/AIVault/_templates/plan.md`
2. Determine a kebab-case slug from the topic
3. Write to `~/AIVault/projects/{project-slug}/plans/YYYY-MM-DD-{slug}.md` (using today's date)
   (or `~/AIVault/general/plans/YYYY-MM-DD-{slug}.md`)
4. Fill in YAML frontmatter: date, tags, status (draft), **execution-status (draft)**, project, type (plan)
   - If this plan originates from an idea file, set `idea:` to the idea wikilink (e.g., `"[[projects/{slug}/ideas/...-idea|Title]]"`)
   - If this plan implements a specific roadmap milestone, set `roadmap-item:` to the milestone text
5. Fill in: Objective, Current State, Proposed Approach, **Implementation Steps** (with `- [ ]` checkboxes), Trade-offs, Acceptance Criteria
6. Use **full-path wiki-links with display names**: `[[path/to/file|Display Name]]`
7. Add a `## Related` section with cross-links to related vault documents
8. Update the relevant `{scope}-home.md` MOC using the format:
   `- **YYYY-MM-DD** [[path/to/YYYY-MM-DD-{slug}|Plan Name]] - One-line summary`
   Insert newest entries at the top of the Plans list
9. **Update the project's state file** (`{slug}-status.md`) `## Plans` table with a new row:
   `| [[path/to/YYYY-MM-DD-{slug}\|Plan Name]] | \`draft\` | One-line summary |`
10. **If originating from an idea file**: update the idea's `status` frontmatter to `planned`, and move its backlog entry to `## Done` with format: `- [x] [[.../ideas/...-idea|Title]] (plan: [[.../plans/...|Plan Name]])`

## Formatting
Reference the `obsidian-markdown` skill for Obsidian-flavored markdown conventions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pindamster) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
