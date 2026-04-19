---
name: new-spec
description: Write or reformat an AI agent specification (rule, skill, or command) using RFC 2119 requirement language Use when this capability is needed.
metadata:
  author: macintacos
---

Write or reformat an AI agent specification file using [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt) key words (MUST, SHOULD, MAY, etc.) to express requirement levels with precise semantics.

Specification files include **rules**, **skills**, and **commands**. All three use RFC 2119 key words to define requirements.

See `~/.ai-guidelines/rules/rfc2119.md` for the full key word definitions.

## Step 1: Determine Document Type and Mode

Ask the user two things:

1. **Document type** — what kind of specification to create or reformat:
   - **Rule** — a policy or constraint that governs agent behavior
   - **Skill** — a Claude skill that defines a reusable workflow (SKILL.md)
   - **Command** — an Augment command that defines a reusable workflow (.md)

2. **Mode** — whether to create or reformat:
   - **Create** — write a new specification from scratch
   - **Reformat** — update an existing file to use RFC 2119 language

If the user provided arguments when invoking the skill, infer both:
- If the argument looks like a file path, use reformat mode and read the file to determine the document type
- Otherwise, treat the argument as a description and ask for the document type

## Step 2: Gather Input

### Create Mode

#### For Rules

Ask the user to describe:
- What behavior the rule governs
- Which agents it applies to (all agents, Claude only, Augment only)
- Any specific requirements they want included

#### For Skills

Ask the user to describe:
- The skill's name (used in frontmatter and directory name)
- What the skill does (one-sentence description for frontmatter)
- The workflow steps the skill should follow
- Any tools or resources the skill needs access to

#### For Commands

Ask the user to describe:
- The command's name (used as the filename)
- What the command does (one-sentence description for frontmatter)
- The workflow steps the command should follow

### Reformat Mode

Read the existing file. Identify:
- The current structure and intent
- Ad-hoc emphasis patterns to convert (e.g., "NEVER" → MUST NOT, "ALWAYS" → MUST, "Do NOT" → MUST NOT, "CRITICAL" → MUST)
- Requirements that lack clear severity levels

## Step 3: Write the Specification

Use the appropriate structure template based on the document type.

### Rule Structure

```markdown
# <Title>

<One-sentence purpose statement describing what this rule governs.>

## <Section>

<Group requirements logically by topic. Within each section, use RFC 2119 key words to express requirement levels.>

### Examples (if helpful)

<Provide examples showing correct and incorrect behavior.>
```

### Skill Structure

```markdown
---
name: <skill-name>
description: <One-sentence description of what this skill does>
---

<Introductory paragraph explaining the skill's purpose and when to use it.>

## Step 1: <Step Title>

<Step description using RFC 2119 key words for requirements.>

## Step 2: <Step Title>

<Step description using RFC 2119 key words for requirements.>

...additional steps as needed...
```

### Command Structure

```markdown
---
description: <One-sentence description of what this command does>
---

<Introductory paragraph explaining the command's purpose and when to use it.>

## Step 1: <Step Title>

<Step description using RFC 2119 key words for requirements.>

## Step 2: <Step Title>

<Step description using RFC 2119 key words for requirements.>

...additional steps as needed...
```

### Formatting Guidelines

- Use RFC 2119 key words in **bold** to indicate requirement levels
- Organize requirements from strictest (MUST/MUST NOT) to most permissive (MAY)
- Each requirement **SHOULD** be a single, clear statement
- Use bullet lists for related requirements under a common heading
- Include examples when the requirement might be ambiguous
- Keep the specification concise — prefer fewer precise statements over many vague ones

### Key Word Selection Guide

| Intent | Key Word |
|--------|----------|
| Absolute requirement, no exceptions | **MUST** |
| Absolute prohibition, no exceptions | **MUST NOT** |
| Strong recommendation, exceptions need justification | **SHOULD** |
| Strong discouragement, exceptions need justification | **SHOULD NOT** |
| Truly optional, either choice is valid | **MAY** |

### Common Conversions from Ad-Hoc Patterns

| Ad-hoc pattern | RFC 2119 equivalent |
|----------------|---------------------|
| NEVER, Do NOT, CRITICAL | **MUST NOT** |
| ALWAYS, REQUIRED | **MUST** |
| Prefer, Recommended, Try to | **SHOULD** |
| Avoid, Discouraged | **SHOULD NOT** |
| Can, Optionally, If desired | **MAY** |

## Step 4: Determine File Location

Based on the document type and target audience:

### Rules

| Audience | Directory |
|----------|-----------|
| All AI agents (Claude + Augment) | `~/.ai-guidelines/rules/` |
| Claude only | `~/.claude/rules/` |
| Augment only | `~/.augment/rules/` |

Chezmoi source paths:
- `managed/dot_ai-guidelines/rules/`
- `managed/private_dot_claude/rules/`
- `managed/dot_augment/rules/`

### Skills

| Agent | Target | Chezmoi Source |
|-------|--------|----------------|
| Claude | `~/.claude/skills/<name>/SKILL.md` | `managed/private_dot_claude/skills/<name>/SKILL.md` |
| Augment | `~/.augment/skills/<name>/SKILL.md` | `managed/dot_augment/skills/<name>/SKILL.md` |

### Commands

| Location | Path |
|----------|------|
| Target | `~/.augment/commands/<name>.md` |
| Chezmoi source | `managed/dot_augment/commands/<name>.md` |

## Step 5: Present for Approval

Display the formatted specification in full and ask the user to review it before writing to disk.

Include:
- The complete specification content
- The target file path
- A note that the user can request changes before writing

Only write the file after the user approves.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/macintacos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
