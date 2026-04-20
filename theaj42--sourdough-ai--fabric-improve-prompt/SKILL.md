---
name: fabric-improve-prompt
description: Improve LLM prompts using prompt engineering best practices. Invoke when user wants to optimize prompts, improve slash commands, or apply prompt engineering. Use when this capability is needed.
metadata:
  author: theaj42
---

# Improve Prompt

Apply prompt engineering best practices to improve an LLM prompt for better, more consistent results.

## Purpose

Take an existing prompt (from a slash command, CLAUDE.md, skill, or ad-hoc use) and improve it using proven prompt engineering strategies:

- Add specificity and context
- Improve structure and formatting
- Add examples (few-shot)
- Clarify output expectations
- Set appropriate persona/role
- Add constraints and guardrails

## Input Sources

User provides one of:
- **Pasted prompt**: Directly in message
- **File reference**: Path to a slash command or pattern file
- **Command name**: Name of existing slash command to improve

## Process

### 1. Get the Prompt

**From pasted text:**
Use the text directly.

**From file:**
Read the file using the Read tool.

**From slash command:**
Read from the user's slash-commands directory.

### 2. Analyze and Improve

Apply prompt engineering principles:

1. **Be Specific**: Add relevant details and constraints
2. **Clear Structure**: Use numbered steps and delimiters
3. **Provide Examples**: Add few-shot examples when helpful
4. **Specify Output**: Define expected format explicitly
5. **Set Persona**: Define expertise when specialized knowledge helps
6. **Add Constraints**: Length limits, what to avoid
7. **Request Reasoning**: "Think step by step" for complex tasks
8. **Reference Material**: Include sources when accuracy matters
9. **Decompose Tasks**: Break complex into subtasks
10. **Edge Cases**: Consider boundary conditions

### 3. Output Format

```markdown
## Analysis

- [Issue 1]: [Description]
- [Issue 2]: [Description]
- [Issue 3]: [Description]

## Improved Prompt

```
[Ready-to-use improved prompt]
```

## Changes Made

- [Change]: [Why it helps]
- [Change]: [Why it helps]

## Usage Notes

[Tips for using effectively]
```

### 4. Optional: Apply Changes

If improving a slash command or pattern file, offer to apply changes:

"Would you like me to update the file with this improved prompt?"

If yes, use Edit tool to update the file while preserving YAML frontmatter and structure.

## Usage Examples

**Improve pasted prompt:**
```
/fabric-improve-prompt

You are a code reviewer. Review this code and tell me if it's good.
```

**Improve existing slash command:**
```
/fabric-improve-prompt /my-command
```

**Improve from file:**
```
/fabric-improve-prompt ~/my-patterns/summarize.md
```

## When NOT to Over-Engineer

- Simple, one-off questions don't need elaborate prompts
- If the original prompt is already good, say so
- Don't add complexity that doesn't improve results
- Preserve original intent - don't change the goal

## Error Handling

**No prompt provided:**
- Ask user to paste a prompt or specify a file/command

**File not found:**
- Report error, suggest checking path

**Already optimized:**
- Note that prompt is already well-structured
- Suggest minor refinements if any


**Philosophy**: Good prompts are the leverage point for AI effectiveness. A 10-minute investment in prompt improvement can save hours of poor results.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/theaj42) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
