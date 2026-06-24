---
name: rl-optimization
description: | Use when this capability is needed.
metadata:
  author: pradeepmouli
---

# RL Optimization Skill

Apply reinforcement learning principles to improve spec-kit prompts and templates.

## When This Skill Activates

This skill provides background awareness when you're:
- Editing `commands/*.md` files (prompt engineering)
- Modifying `extensions/workflows/*/` templates
- Discussing friction points from real-world usage
- Reviewing feedback about workflow effectiveness
- Analyzing how workflows performed in other projects

## Core Principles

### Prompt Effectiveness Criteria

When editing command prompts, ensure they score well on:

| Criterion | Question to Ask |
|-----------|-----------------|
| **Initial Clarity** | Will the agent understand what to do immediately? |
| **Step Sequence** | Are steps in logical order with clear transitions? |
| **Action Specificity** | Are actions concrete and unambiguous? |
| **Output Guidance** | Is the expected output format clear? |
| **Error Recovery** | What happens if something goes wrong? |
| **Completion Signal** | How does the agent know when it's done? |

### Template Effectiveness Criteria

When editing templates, ensure:

| Criterion | Question to Ask |
|-----------|-----------------|
| **Section Utility** | Will each section be filled with useful content? |
| **Logical Order** | Does the structure flow naturally? |
| **Placeholder Clarity** | Are placeholders self-explanatory? |
| **Completeness** | Are all necessary sections present? |
| **Conciseness** | Is there any redundancy to remove? |

## Red Flags to Watch For

### In Prompts

- Vague instructions: "Create the file" → "Create the file with ALL sections from the template"
- Missing error handling: No guidance for when things fail
- Assumed knowledge: References to concepts not explained
- Ambiguous sequences: "Then do X or Y" without criteria for choosing
- No completion criteria: Agent doesn't know when to stop

### In Templates

- Sections that are always skipped (remove or make optional)
- Missing sections users frequently add manually
- Placeholders that confuse more than help
- Redundant information across sections
- Poor ordering that breaks logical flow

## Improvement Patterns

### Pattern: Explicit Over Implicit

```markdown
# Before (implicit)
Fill in the bug report template.

# After (explicit)
Fill in ALL sections of the bug report template. Do not skip any section,
even if information seems redundant. Pay special attention to:
- Reproduction Steps: Must be executable commands
- Root Cause: Use Five Whys analysis
- Prevention: Specific actions, not general statements
```

### Pattern: Guided Decisions

```markdown
# Before (ambiguous)
Choose the appropriate workflow.

# After (guided)
Choose the workflow based on the task:
- Bug with known cause → /speckit.bugfix
- Bug needing investigation → /speckit.bugfix (document investigation in root cause)
- Small improvement (<7 tasks) → /speckit.enhance
- Large feature → /speckit.specify
```

### Pattern: Failure Recovery

```markdown
# Before (no recovery)
Run the tests.

# After (with recovery)
Run the tests. If tests fail:
1. Check if failure is related to your changes
2. If yes, fix and re-run before proceeding
3. If no (pre-existing failure), document in notes and continue
```

## When Making Changes

Before committing prompt or template changes:

1. **Check against criteria** - Score the change on effectiveness criteria
2. **Look for red flags** - Scan for patterns that cause friction
3. **Consider edge cases** - What happens when things go wrong?
4. **Test mentally** - Walk through as if you were the agent
5. **Compare before/after** - Is the improvement clear?

## Suggesting Intakes

When you notice patterns that suggest an RL intake would be valuable:

- User describes repeated friction with a workflow
- Multiple issues reference the same prompt confusion
- A workflow was used extensively in another project
- Post-mortem reveals systemic prompt issues

Suggest: "This sounds like good data for an RL intake. Want me to run `/rl-intake` to capture these patterns?"

## Reference

See `references/prompt-patterns.md` for detailed examples of good and bad patterns.

Full process documentation: `docs/rl-intake-process.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pradeepmouli) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
