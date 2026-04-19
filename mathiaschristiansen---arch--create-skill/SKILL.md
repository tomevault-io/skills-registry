---
name: create-skill
description: Create a new project-specific skill with proper structure and documentation following best practices. Use when this capability is needed.
metadata:
  author: mathiaschristiansen
---

# Create Skill: $ARGUMENTS

Create a new skill following the Skills & Hooks Authoring Guide.

## Skills Authoring Guide

!`cat .arch/guides/SKILLS-AND-HOOKS-GUIDE.md 2>/dev/null | head -150 || echo "Guide not found - see https://code.claude.com/docs/en/skills"`!

## Existing Skills (for reference)

!`ls -la .claude/skills/ 2>/dev/null || echo "No existing skills"`!

## Skill Types

1. **Reference Skills (Knowledge)** - Conventions, patterns, style guides
   - Run inline with conversation
   - Example: `api-conventions`, `testing-patterns`

2. **Task Skills (Actions)** - Step-by-step instructions
   - Often use `disable-model-invocation: true`
   - Example: `deploy`, `generate-component`

3. **Mode Skills (Context Switching)** - Activate a role
   - Example: `architect-mode`, `implementer-mode`

## Your Task

### Step 1: Determine Skill Type

Based on the description, determine if this is:
- **Reference**: Knowledge to apply to work
- **Task**: Specific actions to perform
- **Mode**: Role/context activation

### Step 2: Create Directory Structure

```bash
mkdir -p .claude/skills/<skill-name>
```

Add supporting directories if needed:
- `templates/` - For templates to fill in
- `examples/` - For example outputs
- `scripts/` - For executable utilities
- `context/` - For reference documentation

### Step 3: Create SKILL.md

Use appropriate frontmatter:

```yaml
---
name: <skill-name>
description: <what it does and when to use it>
argument-hint: <expected arguments>  # if takes args
disable-model-invocation: true       # if user-only
allowed-tools: <tool list>           # if restricted
context: fork                        # if runs in subagent
---

# Skill Title

[Instructions here...]

## Dynamic Context (if needed)

!`command to inject current state`!

## Your Task

[Clear instructions for what to do]
```

### Step 4: Add Supporting Files

If the skill needs:
- Templates: Create in `templates/` directory
- Examples: Create in `examples/` directory
- Scripts: Create in `scripts/` directory (make executable)
- Context: Create in `context/` directory

Reference them in SKILL.md:
```markdown
## Template
$FILE{templates/example-template.md}
```

### Step 5: Test the Skill

After creating:
1. Invoke with `/skill-name` to test
2. Verify it appears in skill list
3. Test with various inputs
4. Adjust based on results

### Step 6: Document Usage

Add to SKILL.md:
- Example invocations
- Expected behavior
- Common use cases

## Best Practices Checklist

- [ ] Name is lowercase with hyphens
- [ ] Description explains WHEN to use it
- [ ] Dynamic context injects current state
- [ ] Instructions are clear and actionable
- [ ] Supporting files are referenced properly
- [ ] Frontmatter options are appropriate
- [ ] Tested with example inputs

## Output

After creating, provide:
- Skill name and type
- Directory structure created
- Files created
- How to invoke
- Example usage

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mathiaschristiansen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
