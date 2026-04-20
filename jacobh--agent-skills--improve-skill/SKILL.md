---
name: improve-skill
description: Extract learnings from session transcripts or notes and incorporate them into existing skills or create new ones. Use when the user has analysis documents, session learnings, or patterns they want to formalize into reusable skill content. Use when this capability is needed.
metadata:
  author: jacobh
---

# Improve Skill

Transforms session learnings, analysis documents, and discovered patterns into structured skill improvements.

## When to Use

- User has notes/documents with learnings from a deep work session
- User wants to update an existing skill with new patterns discovered
- User wants to create a new skill from accumulated knowledge
- User says "improve skill", "update skill", "add to skill", "skill learnings"

## Process

### 1. Gather Context

Identify the inputs:
- **Learnings source**: Session notes, analysis docs, conversation history
- **Target skill**: Existing skill to improve, or create new
- **Scope**: What categories of learnings (patterns, anti-patterns, workflows, reference data)

**If the user references a past session** (e.g. "the session where we did X", "the session on branch Y"), you'll need to locate and read the session transcript from disk. See [SESSION-FILES.md](SESSION-FILES.md) for how to find and read Claude Code session files.

If the user hasn't specified a target skill, ask:
- Is this for an existing skill? Which one?
- Or should this become a new skill?

### 2. Read Existing Skill (if improving)

Read the target skill's files:
```bash
ls -la <skill-directory>/
cat <skill-directory>/SKILL.md
# Read any additional reference files
```

Understand the current structure before proposing changes.

### 3. Extract Learnings

From the source documents, identify:

| Category | What to Extract |
|----------|-----------------|
| **Patterns** | Recurring solutions, resolution strategies, code idioms |
| **Anti-patterns** | Common mistakes, things that don't work |
| **Classifications** | Module categories, conflict types, file groupings |
| **Reference data** | Tables, mappings, inventories |
| **Workflows** | Step-by-step procedures, checklists |
| **Verification** | Commands to validate, checks to run |

### 4. Decide on Structure

**For small additions** (< 50 lines):
- Add directly to SKILL.md in appropriate section

**For substantial additions** (50-200 lines):
- Create new section in SKILL.md with summary
- Consider separate reference file if content is lookup-oriented

**For large additions** (> 200 lines):
- Create separate .md file (e.g., MODULES.md, PATTERNS.md)
- Add brief reference in SKILL.md pointing to the new file
- Use progressive disclosure pattern

### 5. Apply Updates

Follow these principles:

1. **Preserve existing structure** - Add to existing sections when possible
2. **Use consistent terminology** - Match the skill's existing voice/terms
3. **Keep SKILL.md scannable** - Move reference tables to separate files
4. **Add context on when to use** - New content should explain its trigger
5. **Update description if needed** - If scope changed, update frontmatter

### 6. Verify

After making changes:
```bash
# Check file exists and is valid
cat <skill-directory>/SKILL.md | head -20

# Verify frontmatter is valid YAML
# - name: lowercase, hyphens, max 64 chars
# - description: non-empty, max 1024 chars

# Check for broken internal links
rg "\[.*\]\(.*\.md\)" <skill-directory>/
```

## Output Format

Present changes as:

1. **Summary** - What was added/changed
2. **Rationale** - Why this structure was chosen
3. **Files modified** - List of files changed
4. **Skill description update** - If the description should change

## Guidelines

- **Be concise**: Only add what Claude doesn't already know
- **Prefer tables**: For reference data, classifications, mappings
- **Use examples**: Concrete over abstract
- **One level deep**: Reference files should link from SKILL.md, not from each other
- **Test mentally**: Would this help Claude in a future session?

## Creating New Skills

If creating a new skill rather than improving existing:

1. Choose location:
   - `~/.claude/skills/<name>/` - Personal, all projects
   - `.claude/skills/<name>/` - Project-specific

2. Create directory and SKILL.md:
   ```bash
   mkdir -p <location>/<skill-name>
   ```

3. Write SKILL.md with:
   ```markdown
   ---
   name: <lowercase-with-hyphens>
   description: <what it does and when to trigger, max 1024 chars>
   ---

   # <Skill Title>

   <Brief description>

   ## When to Use
   <Trigger conditions>

   ## Process
   <Step-by-step workflow>

   ## Guidelines
   <Key principles>
   ```

4. Add reference files as needed for large content blocks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jacobh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
