---
name: skill-builder
description: Guide users through creating new Claude Code Skills with proper structure, validation, and best practices Use when this capability is needed.
metadata:
  author: womendefiningai
---

# Skill Builder

This skill helps you create well-structured Claude Code Skills. Use it when the user wants to build a new skill for Claude Code.

## When to Use This Skill

Invoke this skill when users:
- Want to create a new Claude Code Skill
- Ask how to build or structure a skill
- Need help with skill frontmatter or organization
- Want to validate an existing skill structure

## Skill Creation Workflow

### Step 1: Understand the Requirements

Ask the user these questions (use AskUserQuestion tool when appropriate):
1. **What will the skill do?** - Get a clear description of the skill's purpose
2. **When should it trigger?** - Understand what user requests should activate it
3. **What's the scope?** - Is it project-specific or personal/general-purpose?
4. **What resources are needed?** - Templates, scripts, reference data, etc.

### Step 2: Determine Skill Location

Based on scope, choose the directory:
- **Project-specific**: `.claude/skills/[skill-name]/` (within project root)
- **Personal/general**: `~/.claude/skills/[skill-name]/` (available everywhere)

### Step 3: Create Directory Structure

Use this pattern:
```
skill-name/
├── SKILL.md           (required: main instructions)
├── REFERENCE.md       (optional: detailed reference info)
├── FORMS.md           (optional: templates and forms)
├── scripts/           (optional: executable code)
│   ├── script1.py
│   └── script2.sh
└── resources/         (optional: templates, data files)
    └── template.txt
```

### Step 4: Write SKILL.md

The SKILL.md must follow this structure:

```markdown
---
name: skill-name
description: Clear description of what this skill does and when to use it
---

# Skill Name

Main instructions for Claude go here.

## When to Use

Describe scenarios where this skill should be invoked.

## How It Works

Step-by-step instructions for Claude to follow.

## Examples

Provide examples of usage.
```

**Frontmatter Requirements:**
- `name`: Max 64 chars, lowercase letters/numbers/hyphens only (e.g., "excel-automation")
- `description`: Non-empty, max 1024 chars, should clearly indicate when to use the skill
  - **IMPORTANT:** Include BOTH natural language AND technical keywords
  - Natural language: How non-technical users would describe it ("fix broken code", "save data", "put online")
  - Technical terms: Jargon that developers use ("debug", "database schema", "deploy")
  - Example: "Auto-invoked when user wants to fix broken code, not working, or crashed. Also triggers on debug, stack trace, error analysis."

**Best Practices for Instructions:**
- Write like an "onboarding guide for a new team member"
- Use clear, step-by-step procedures
- Include contextual examples
- Reference supporting files instead of embedding everything
- Keep instructions under 5k tokens for efficient loading
- Use progressive disclosure: reference detailed info in REFERENCE.md

### Step 5: Add Supporting Files (Optional)

**REFERENCE.md**: Detailed technical information that Claude can read when needed
- API documentation
- Detailed specifications
- Comprehensive examples
- Technical constraints

**FORMS.md**: Templates and structured formats
- Form templates
- Output format examples
- Structured data patterns

**scripts/**: Executable code for complex operations
- Python scripts for data processing
- Shell scripts for automation
- Utilities that Claude can invoke via bash

**resources/**: Static files and templates
- Document templates
- Sample data
- Reference materials

### Step 6: Validate the Skill

Check these requirements:
- [ ] `SKILL.md` exists with valid YAML frontmatter
- [ ] `name` is lowercase, alphanumeric with hyphens, max 64 chars
- [ ] `description` is clear, non-empty, max 1024 chars
- [ ] **Description includes natural language keywords** (not just technical jargon)
- [ ] Instructions are clear and actionable
- [ ] File references are correct
- [ ] No internet/API dependencies (Claude Code constraint)
- [ ] Only uses pre-installed packages

**Run automated validation:**
```bash
python .validation/validate-skills.py
```

**Validation Scoring (29 points total):**
- SKILL.md exists: 1 point
- Valid frontmatter: 3 points
- Name validation: 2 points
- Description validation: 3 points
- Content structure: 5 points
- Token count <5000: 2 points
- Supporting files: 3 points
- Scripts directory: 2 points
- Industry standards: 3 points
- Examples: 2 points
- **Natural language keywords: 3 points** (NEW)

**Passing threshold:** 80% (24/29 points)

### Step 7: Test the Skill

After creation:
1. Verify the skill is discoverable (Claude should see it in metadata)
2. Test with a sample task that should trigger it
3. Ensure resources load correctly when referenced
4. Check that instructions are clear and complete

## Progressive Disclosure Strategy

Design skills to minimize token usage:

1. **Always Loaded (Metadata)**: ~100 tokens
   - Just the YAML frontmatter

2. **Loaded When Triggered (Instructions)**: <5k tokens
   - Core procedures from SKILL.md body

3. **Loaded As Needed (Resources)**: No token cost
   - Files Claude reads via bash commands
   - Scripts Claude executes when required
   - Templates Claude accesses on demand

## Common Skill Patterns

### Document Generation Skill
```
doc-generator/
├── SKILL.md          (generation instructions)
├── templates/        (document templates)
└── scripts/          (formatting scripts)
```

### Code Analysis Skill
```
code-analyzer/
├── SKILL.md          (analysis procedures)
├── REFERENCE.md      (pattern definitions)
└── scripts/          (analysis tools)
```

### Automation Skill
```
automation-helper/
├── SKILL.md          (automation workflows)
├── FORMS.md          (configuration templates)
└── scripts/          (automation scripts)
```

## Implementation Steps

When helping users build a skill:

1. **Gather Information**: Use AskUserQuestion to understand requirements
2. **Plan Structure**: Determine files needed based on complexity
3. **Create Directory**: Make the skill directory in appropriate location
4. **Write SKILL.md**: Create with proper frontmatter and instructions
5. **Add Supporting Files**: Create REFERENCE.md, FORMS.md, scripts as needed
6. **Validate**: Check all requirements are met
7. **Document**: Explain to user how to test and use the skill
8. **Show Location**: Provide the full path to the created skill

## Validation Checklist

Before completing, verify:
- [ ] Directory created in correct location
- [ ] SKILL.md has valid frontmatter
- [ ] Name follows naming rules
- [ ] Description is clear and helpful
- [ ] Instructions are actionable
- [ ] Examples are provided
- [ ] Supporting files are organized logically
- [ ] No external dependencies
- [ ] File paths in instructions are correct

## Tips for Great Skills

1. **Clear Descriptions**: Make it obvious when Claude should use the skill
2. **Step-by-Step**: Write procedures as numbered steps
3. **Examples First**: Show examples before explaining
4. **Minimize Context**: Use progressive disclosure extensively
5. **Self-Contained**: Don't assume external resources are available
6. **Tested Procedures**: Ensure instructions actually work
7. **Helpful Metadata**: Write descriptions that help with discovery

## Common Mistakes to Avoid

- Using uppercase or special characters in skill name
- Making descriptions too vague or too long
- Embedding all content in SKILL.md instead of using supporting files
- Referencing non-existent files
- Assuming internet access or external APIs
- Creating overly complex skills that should be split
- Forgetting to include "when to use" guidance

## Reference Files

See the templates directory for:
- Basic skill template
- Advanced skill template with supporting files
- Example skills for common use cases

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/womendefiningai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
