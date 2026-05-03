---
name: curate-docs
description: Curate project documentation after completing a feature. Updates CLAUDE.md, learnings/, creates/updates skills, and maintains docs/. Invoke after any significant work. Use when this capability is needed.
metadata:
  author: evoleinik
---

> **Optional:** Get auto-reminded to run this after 1+ commits on feature branches.
> [Set up the hook](https://github.com/evoleinik/curate-docs#optional-auto-reminder-hook)

# Curate Documentation

Run this after completing a feature to capture learnings for the next session.

## Step 1: Gather Context

First, understand what was done:

```bash
# Recent commits
git log --oneline -10

# Files changed in last few commits
git diff --stat HEAD~3

# Current CLAUDE.md
cat CLAUDE.md | head -100

# Current learnings (if project has learnings/ folder)
ls learnings/ 2>/dev/null && wc -l learnings/*.md
```

## Step 2: Identify What to Capture

Ask yourself these questions about the work just completed:

### For CLAUDE.md (essential, every-session context)
- [ ] New commands that will be used frequently?
- [ ] New critical gotchas (production breaks, data loss)?
- [ ] New key files that are central to the codebase?
- [ ] Changed merchants, models, or configuration?

### For learnings/ (searchable topic files)
- [ ] New gotchas for a specific tool/service? (Stripe, database, Vercel, etc.)
- [ ] CLI commands or patterns for a domain?
- [ ] Troubleshooting steps that might be needed again?

### For Skills (on-demand workflows)
- [ ] New multi-step workflow discovered? (debugging, deployment, testing)
- [ ] Procedure that requires specific sequence of steps?
- [ ] Task that will be repeated but not every session?

### For docs/ (reference material)
- [ ] Architecture decisions that need explanation?
- [ ] System design that's not obvious from code?
- [ ] API contracts or data flows?

## Step 3: Categorize Learnings

Use this decision tree:

```
Is this a CRITICAL gotcha (production breaks, data loss)?
  YES → CLAUDE.md "Critical Gotchas" section (1-liner with pointer to learnings/)
  NO → Is this tool/service-specific knowledge?
    YES → learnings/<topic>.md (searchable via grep)
    NO → Is this a repeatable workflow/procedure?
      YES → Create/update a skill in .claude/skills/
      NO → Is this reference material for deep understanding?
        YES → Add to docs/
        NO → Don't document (it's in the code or not worth capturing)
```

## Step 4: Update Documentation

### CLAUDE.md Rules
- Max ~100-150 lines (trim aggressively)
- Tables over prose
- Commands over explanations
- Critical Gotchas = things that cause production breaks or data loss
- Point to learnings/ for details: `(see learnings/stripe.md)`

### learnings/ Rules (if project uses this structure)
- One file per tool/service (stripe.md, database.md, vercel.md)
- Flat bullets, searchable via `grep -r "webhook" learnings/`
- Include CLI commands with examples
- Group by section (## Setup, ## Gotchas, ## CLI Reference)
- NOT needed every session - that's what makes it different from CLAUDE.md

### Skill Rules
- One skill per workflow
- Start with `---` frontmatter (name, description)
- Step-by-step with code blocks
- Include troubleshooting section

### docs/ Rules
- One file per topic
- Can be longer/detailed
- Include diagrams if helpful
- Link from CLAUDE.md or skills

## Step 5: Curate, Don't Append

**CRITICAL**: Don't just add - also remove and refactor:

- [ ] Remove outdated information (fixed bugs, old workarounds)
- [ ] Merge duplicates
- [ ] Move detailed content from CLAUDE.md → learnings/
- [ ] Delete skills for one-time tasks
- [ ] Keep CLAUDE.md under 150 lines
- [ ] Each item must earn its place - if unsure, don't add it

### What Qualifies
- Error solutions specific to this project
- Non-obvious commands or workflows
- Gotchas that wasted time
- File locations that were hard to find

### What Does NOT Qualify
- Generic programming knowledge
- One-time issues unlikely to recur
- Things already in README or docs
- Verbose explanations
- Fixes already in code (no need to document solved problems)

## Step 6: Commit

```bash
git add CLAUDE.md learnings/ .claude/skills/ docs/
git commit -m "docs: curate learnings from [feature name]"
git push
```

## Example Outputs

### CLAUDE.md Critical Gotcha (1-liner with pointer)
```markdown
## Critical Gotchas

**Database** (see `learnings/database.md`):
- Dropping columns: 3-step process - (1) remove from schema, (2) deploy, (3) THEN drop
```

### learnings/ Entry (searchable details)
```markdown
# Database Learnings

## CRITICAL - Production Safety

### Dropping Columns (3-step process)

1. Remove column from `schema.prisma`
2. Merge PR → Vercel deploys → Prisma client regenerates WITHOUT the column
3. ONLY THEN drop column from DB manually

**If you drop column first, production breaks** - Prisma client still expects it.
```

### New Skill
```markdown
---
name: new-workflow
description: When to use this workflow
---

# New Workflow

## Step 1: Do X
...
```

## Anti-Patterns

- Don't document things that are obvious from code
- Don't create skills for one-time tasks
- Don't add to CLAUDE.md if it's only needed occasionally (use learnings/)
- Don't write prose when a table works
- Don't keep outdated information "just in case"
- Don't duplicate content between CLAUDE.md and learnings/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/evoleinik) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
