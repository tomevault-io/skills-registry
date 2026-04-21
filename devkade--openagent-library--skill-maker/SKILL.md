---
name: skill-maker
description: Create new Claude Code agent skills following Anthropic's best practices Use when this capability is needed.
metadata:
  author: devkade
---

# Skill Maker

Create well-designed Claude Code agent skills following Anthropic's official best practices.

## When to Use

- User says "create a skill", "make a skill", "new skill"
- User wants to automate a repetitive workflow
- User asks to turn a process into a reusable skill

## Workflow

### Phase 1: Requirements Gathering

Ask these questions (skip if already provided):

1. **Purpose**: What specific task should this skill handle?
2. **Trigger**: What phrases/situations should activate this skill?
3. **Scope**: What should this skill NOT do? (boundaries)
4. **Success Criteria**: How do we know the skill worked correctly?

### Phase 2: Design Validation

Before writing any files, validate:

| Check                  | Question                                                                |
| ---------------------- | ----------------------------------------------------------------------- |
| Single Responsibility  | Does it focus on ONE role/problem type?                                 |
| Discoverability        | Is the name+description specific enough for Claude to choose correctly? |
| Progressive Disclosure | Can we split content into layers (core vs reference)?                   |

### Phase 3: Skill Creation

#### Required Files

```
skill-name/
├── SKILL.md          # Required: Core instructions
├── references/       # Optional: Detailed docs, specs
├── scripts/          # Optional: Executable code
└── assets/           # Optional: Templates, examples
```

#### SKILL.md Template

```markdown
---
name: { skill-name }
description: { When and what - specific enough for Claude to decide activation }
---

# {Skill Title}

{1-2 sentence overview}

## When to Use

- {Trigger phrase 1}
- {Trigger phrase 2}
- {Condition that activates this skill}

## When NOT to Use

- {Exclusion 1}
- {Exclusion 2}

## Workflow

### Step 1: {First Action}

{Clear, explicit instructions}

### Step 2: {Second Action}

{No ambiguous language like "if possible" or "try to"}

## Decision Rules

| Condition     | Action |
| ------------- | ------ |
| {Condition A} | Do X   |
| {Condition B} | Do Y   |
| Default       | Do Z   |

## Error Handling

| Error          | Recovery                   |
| -------------- | -------------------------- |
| {Error type 1} | {Specific recovery action} |
| {Error type 2} | {Specific recovery action} |

## Examples

### Example 1: {Typical Case}

Input: {example input}
Expected: {expected behavior}

### Example 2: {Edge Case}

Input: {edge case input}
Expected: {expected behavior}
```

### Phase 4: Placement

Ask user for skill scope:

| Scope         | Location                   | Effect                    |
| ------------- | -------------------------- | ------------------------- |
| Global (user) | `~/.claude/skills/{name}/` | Available in all projects |
| Project       | `.claude/skills/{name}/`   | Only this project         |

### Phase 5: Validation (Auto-Checklist)

**MANDATORY**: After creating skill files, automatically run validation.

#### Step 5.1: Load Full Checklist

```
Read: ~/.claude/skills/skill-maker/references/checklist.md
```

#### Step 5.2: Validate Against All 10 Categories

Run through EVERY category in checklist.md:

1. Required Structure (6 items)
2. Progressive Disclosure (6 items)
3. Evaluation-Based Design (4 items)
4. Claude's Perspective (6 items)
5. Code Execution (6 items) - N/A if no scripts
6. Iterative Refinement (5 items)
7. Practical Validation (4 items) - Mark pending if not yet tested
8. Security (4 items)
9. Example Design (3 items)
10. Explicit Rules (3 items)

#### Step 5.3: Output Validation Report

```markdown
## Skill Validation Report: {skill-name}

| Category               | Pass | Warn | Pending |
| ---------------------- | ---- | ---- | ------- |
| Required Structure     | X/6  |      |         |
| Progressive Disclosure | X/6  |      |         |
| ...                    |      |      |         |

### Issues Found

| #   | Item           | Status | Fix Required        |
| --- | -------------- | ------ | ------------------- |
| 4.2 | Domain in name | ⚠️     | Consider pkm-{name} |

### Recommended Fixes

1. {Specific fix 1}
2. {Specific fix 2}
```

#### Step 5.4: Apply Fixes

If issues found:

1. Apply fixes automatically (structure, changelog, examples)
2. Re-validate after fixes
3. Report final status

#### Quick Validation (Inline)

For quick reference without loading full checklist:

##### Required Structure

- [ ] SKILL.md exists at skill root
- [ ] YAML frontmatter has `name` field
- [ ] YAML frontmatter has `description` field
- [ ] Description enables Claude to decide activation

##### Progressive Disclosure

- [ ] Level 1: name + description conveys purpose
- [ ] Level 2: SKILL.md body has core instructions
- [ ] Level 3: Details in separate reference files

##### Design Quality

- [ ] Single responsibility (one role/problem type)
- [ ] No ambiguous language ("if possible", "try to", "when appropriate")
- [ ] Explicit decision rules (condition → action)
- [ ] Error handling specified
- [ ] Examples included (typical + edge case)
- [ ] Changelog with version info

## Anti-Patterns to Avoid

| Anti-Pattern           | Problem                         | Fix                          |
| ---------------------- | ------------------------------- | ---------------------------- |
| Vague scope            | Claude can't decide when to use | Add specific trigger phrases |
| Everything in SKILL.md | Token waste, context pollution  | Split into references/       |
| Ambiguous instructions | Unpredictable behavior          | Use explicit decision tables |
| No error handling      | Claude gets stuck on failures   | Add recovery strategies      |
| No examples            | Misinterpretation of intent     | Add typical + edge cases     |

## References

For detailed design principles, read:

- `references/design-principles.md` - Full design philosophy
- `references/checklist.md` - Complete validation checklist

---

## Changelog

### v1.0.0 (2026-01-07)

- Initial release
- 5-phase workflow: Requirements → Design Validation → Creation → Placement → Validation
- SKILL.md template with decision tables, error handling, examples
- References: design-principles.md, checklist.md
- Anti-patterns documentation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/devkade) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
