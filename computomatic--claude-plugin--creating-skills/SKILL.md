---
name: creating-skills
description: Creates new Claude Code skills with best practices. Use when the user wants to create a skill, add a slash command, or automate a workflow.
metadata:
  author: computomatic
---

# Create Skill

Guide the user through creating a highly effective Claude Code skill.

**Important:** Use "ultrathink" extended thinking for skill design decisions.

## Workflow

### Phase 1: Discovery

Ask the user clarifying questions to understand the skill requirements:

1. **Purpose:** What task or workflow should this skill handle?
2. **Scope:** Should it be project-specific (`./skills/`) or personal (`~/.claude/skills/`)?
3. **Invocation:** Should Claude invoke it automatically, or only when the user explicitly calls it?
4. **Output:** What output format or artifacts are expected?
5. **Frequency:** How often will this skill be used?

If the available context makes any of this information obvious, there's no need to ask redundantly. However, clarify any ambiguity rather than making assumptions.
If the user provided arguments, use them to inform the skill name and purpose.

### Phase 2: Design

Determine the skill type and structure:

**Skill Types:**
- **Reference content:** Conventions, patterns, domain knowledge (Claude references as needed)
- **Task content:** Step-by-step workflow with clear phases (Claude follows the steps)
- **Hybrid:** Combines reference material with workflow guidance

**Naming:**
- Lowercase with hyphens
- Be specific: `formatting-sql` not `sql-stuff`
- Always include a `name` field in frontmatter -- without it, the skill name is derived from the directory and may be inconsistently prefixed by the plugin name

**Frontmatter Options:**
```yaml
---
name: skill-name
description: Does X when Y. Use for Z.
argument-hint: "[required-arg] [optional: arg]"
disable-model-invocation: true  # Set true if user should control invocation
---
```

**Description Guidelines:**
- Write in third person ("Creates..." not "Create...")
- Include WHAT the skill does AND WHEN to use it
- Use specific trigger words users might say
- Never use vague descriptions like "helps with documents"

Good: `Generates mermaid diagrams from technical descriptions. Use when the user wants to visualize architecture, data flows, or processes.`

Bad: `Helps with diagrams.`

### Phase 3: Structure

Design the skill content structure:

**For Reference Skills (~100-300 lines):**
```markdown
# Skill Name

Brief overview.

## Conventions
- Key convention 1
- Key convention 2

## Patterns
### Pattern Name
Description and example.

## Examples
Concrete examples of correct usage.
```

**For Task Skills (~150-400 lines):**
```markdown
# Skill Name

Brief overview of what this accomplishes.

## Workflow

### Step 1: Gather Context
What to collect and understand first.

### Step 2: Execute
The main actions to perform.

### Step 3: Verify
How to confirm success.

## Guidelines
Key principles to follow throughout.

## Examples
Sample inputs and outputs.
```

**For Complex Skills (use supporting files):**
```
skills/
  skill-name/
    SKILL.md           # Main file, under 500 lines
    conventions.md     # Reference material
    templates/         # Template files
      template-a.md
```

Reference supporting files with relative paths: `See [conventions](./conventions.md)`

### Phase 4: Content Principles

Apply these principles when writing skill content:

**Conciseness is Critical:**
- The skill shares context window with the conversation
- Every line should earn its place
- Remove anything Claude already knows

**Degrees of Freedom:**
- **High freedom:** Guidelines and principles, Claude decides specifics
- **Medium freedom:** Clear steps with room for judgment
- **Low freedom:** Exact templates and formats, minimal deviation

Match freedom level to the task. Code formatting needs low freedom; creative tasks need high freedom.

**What to Include:**
- Project-specific conventions Claude cannot infer
- Workflow steps in the order they should happen
- Examples of correct output
- Common pitfalls and how to avoid them

**What to Exclude:**
- General knowledge Claude already has
- Obvious instructions ("write clean code")
- Time-sensitive information that will become stale
- Lengthy explanations when examples suffice

**Formatting:**
- Use consistent terminology throughout
- Number workflow steps
- Use code blocks for templates and examples
- Keep reference sections scannable with headers

### Phase 5: Implementation

Create the skill files:

1. Determine the full path:
   - Personal: `~/.claude/skills/{skill-name}/SKILL.md`
   - Project: `./{project-root}/skills/{skill-name}/SKILL.md`

2. Create the directory structure

3. Write SKILL.md with:
   - Frontmatter (name, description, argument-hint if needed, disable-model-invocation if needed)
   - Body content following the appropriate structure

4. Create supporting files if the skill is complex

### Phase 6: Verification

After creating the skill:

1. **Autocomplete Check:** Type `/` and verify the skill appears in the list
2. **Invocation Test:** Run the skill with a sample task
3. **Output Review:** Confirm the skill produces expected results
4. **Edge Cases:** Test with minimal input and unusual requests

If issues arise:
- Check frontmatter syntax (YAML is sensitive to formatting)
- Verify file is named exactly `SKILL.md`
- Ensure description clearly indicates when to use the skill

## Quick Reference

### Frontmatter Template
```yaml
---
name: {skill-name}
description: {What it does}. Use when {trigger conditions}.
argument-hint: "[args]"
disable-model-invocation: {true if user-controlled}
---
```

### Common Patterns

**Skill that processes input:**
```markdown
## Workflow
1. Parse the provided {input-type}
2. Apply {transformation}
3. Output {result-format}
```

**Skill that creates artifacts:**
```markdown
## Workflow
1. Gather requirements from user
2. Design {artifact-type} structure
3. Create the files
4. Verify correctness
```

**Skill that enforces conventions:**
```markdown
## Conventions
- {Convention with rationale}

## Examples
### Correct
{example}

### Incorrect
{counter-example}
```

### Checklist

Before finalizing a skill, verify:

- [ ] Frontmatter includes explicit `name` field
- [ ] Name uses lowercase with hyphens
- [ ] Description says WHAT and WHEN
- [ ] Content is under 500 lines (or uses supporting files)
- [ ] Workflow steps are numbered and clear
- [ ] Examples demonstrate expected output
- [ ] No redundant information Claude already knows
- [ ] Consistent terminology throughout
- [ ] Appropriate degree of freedom for the task

## Examples

### Example: Simple Reference Skill

```markdown
---
name: formatting-sql
description: Formats SQL queries according to project conventions. Use when writing or reviewing SQL.
---

# SQL Formatting

## Conventions
- Keywords in UPPERCASE
- Table names in snake_case
- Indent nested clauses by 2 spaces
- One column per line in SELECT

## Example

```sql
SELECT
  user_id,
  created_at,
  email
FROM
  users
WHERE
  status = 'active'
ORDER BY
  created_at DESC
```
```

### Example: Task Skill with Workflow

```markdown
---
name: reviewing-pull-requests
description: Reviews pull requests for code quality and correctness. Use when the user asks to review a PR or check code changes.
argument-hint: "[pr-number or branch]"
---

# Pull Request Review

## Workflow

### Step 1: Gather Context
- Fetch the PR diff
- Read related files for context
- Check for linked issues

### Step 2: Review
Examine changes for:
- Correctness: Does the code do what it claims?
- Style: Does it follow project conventions?
- Tests: Are changes adequately tested?
- Security: Any vulnerabilities introduced?

### Step 3: Provide Feedback
- List issues by severity (blocking, suggestion, nit)
- Include specific line references
- Suggest concrete fixes

## Guidelines
- Be constructive, not critical
- Acknowledge what's done well
- Prioritize blocking issues
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/computomatic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
