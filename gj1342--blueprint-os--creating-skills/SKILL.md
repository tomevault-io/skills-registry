---
name: creating-skills
description: Acquires or creates Blueprint OS skills. Searches skills.sh registry first, installs and customizes if found, creates from scratch as fallback. Use when the user asks to create a skill, add a capability, find a skill, or automate a repeatable agent task. Use when this capability is needed.
metadata:
  author: gj1342
---

# Creating Skills

## When to use this skill

- User asks to "create a skill for [task]" or "find a skill for [task]"
- A repeatable workflow needs to be captured and reused
- An existing skill needs to be refactored or extended
- User wants to document a process the agent should follow consistently

## Workflow

- [ ] Clarify the skill's purpose — what task does it solve, and what are its triggers?
- [ ] Search skills.sh for an existing community skill (see Instructions > Discovering on skills.sh)
- [ ] If found: install with `npx skills add <owner/repo> -a antigravity -y --copy` and evaluate fit
- [ ] If it covers the need as-is: done — update the Skills Index in `README.md`
- [ ] If it partially fits: customize it (see Instructions > Customizing an installed skill)
- [ ] If nothing suitable found: create from scratch (see Instructions > Creating from scratch)
- [ ] Save to `.agent/skills/<skill-name>/SKILL.md`
- [ ] Update the Skills Index in `README.md`

## Instructions

### Discovering on skills.sh

Before building anything, search the [skills.sh registry](https://skills.sh) — it has 200+ community skills covering frontend, backend, testing, APIs, databases, and more.

**Search via agent:**

```bash
npx skills add https://github.com/vercel-labs/skills --skill find-skills -a antigravity -y --copy
```

Then ask the `find-skills` skill to search for what you need.

**Browse directly:** [skills.sh](https://skills.sh) — filter by category or search by keyword.

**Install a skill:**

```bash
npx skills add <owner/repo> -a antigravity -y --copy
```

Example:

```bash
npx skills add supabase/agent-skills -a antigravity -y --copy
npx skills add vercel-labs/agent-skills -a antigravity -y --copy
npx skills add anthropics/skills -a antigravity -y --copy
```

The `-a antigravity` flag installs to `.agent/skills/`, matching Blueprint OS. The `--copy` flag creates real files (not symlinks) so deleting `.agents` won't break the skill when Cursor is detected.

### Customizing an installed skill

After installing, open the skill's `SKILL.md` and evaluate:

- **Keep**: the core workflow steps and any low-freedom (exact command) sections
- **Adjust**: naming conventions, paths, and tool-specific references to match your project
- **Add**: project-specific context in a new `## Project context` section at the bottom
- **Remove**: sections irrelevant to your stack

When customizing, save edits directly to the installed `SKILL.md`. Do not rename the file — only update its content.

If the changes are substantial enough that the skill diverges significantly from upstream, rename the folder to reflect your custom version (e.g., `supabase-postgres/` → `postgres-migrations/`).

### Creating from scratch

Use this path only when skills.sh has nothing suitable.

**Naming**

- `name` field: gerund form, lowercase, hyphens only, max 64 chars
  - Good: `reviewing-code`, `managing-databases`, `deploying-to-production`
  - Bad: `code-review`, `database`, `deploy`
- Folder name must match the `name` field exactly

**Description**

- Written in third person
- Must state what it does AND when to trigger it (keywords/phrases)
- Max 1024 chars
- Example: "Reviews code changes for quality, security, and style. Use when the user asks for a code review, PR review, or feedback on an implementation."

**Body structure**

```markdown
---
name: [gerund-name]
description: [third-person description with triggers]
---

# [Skill Title]

## When to use this skill
- [Specific trigger condition]
- [Another trigger]

## Workflow
- [ ] [Step 1]
- [ ] [Step 2]

## Instructions
[Logic, rules, code examples — tailored to the task]

## Resources
- [Link to scripts/ or examples/ if applicable]
```

**Degrees of freedom**

Match the instruction style to how much autonomy the agent should have:

- **High freedom** (exploration, design decisions) → use bullet points with heuristics
- **Medium freedom** (structured but flexible) → use code block templates the agent fills in
- **Low freedom** (fragile operations, exact steps required) → use specific bash commands or exact sequences

**File size**

Keep `SKILL.md` under 500 lines. If more detail is needed, create a secondary file and link to it:

```markdown
[See ADVANCED.md](ADVANCED.md)
```

Only one level of linking. Do not nest further.

### Path conventions

Always use forward slashes `/` for paths, never backslashes `\`.

## Resources

- Community registry: [skills.sh](https://skills.sh)
- skills.sh guide: `adapters/skills-sh.md`
- Blueprint OS skill examples: `.agent/skills/`
- Standards guide: `standards/README.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gj1342) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
